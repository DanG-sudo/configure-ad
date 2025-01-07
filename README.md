<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

1. Prepare Azure Infrastructure
a. Create a Resource Group

    Sign in to the Azure Portal.
    Go to Resource Groups and click + Create.
    Enter a name (e.g., ADDeployment-RG) and select a region.
    Click Review + Create > Create.

b. Set Up a Virtual Network

    Navigate to Virtual Networks and click + Create.
    Choose the resource group created earlier.
    Name the virtual network (e.g., AD-VNet).
    Define an address space (e.g., 10.0.0.0/16) and add a subnet for domain controllers (e.g., 10.0.0.0/24).
    Click Review + Create > Create.

c. Deploy Virtual Machines

    Go to Virtual Machines and click + Create.
    Select the resource group and name the VM (e.g., AD-DC1).
    Choose a region (same as the virtual network) and a size (e.g., Standard D2s_v3).
    Set Windows Server as the image (e.g., Windows Server 2022 Datacenter).
    Configure Admin Account credentials (e.g., AdminUsername and AdminPassword).
    Under Networking, select the virtual network (AD-VNet) and subnet.
    Disable Public IP (for better security) and ensure connectivity through a VPN or ExpressRoute.
    Repeat to create a second VM (e.g., AD-DC2) for redundancy.

2. Configure Virtual Machines for AD Deployment
a. Set Static IPs

    Navigate to each VM's Networking section in the Azure portal.
    Under NIC Settings, configure a static private IP for each VM to ensure they retain their IP addresses.

b. Enable Inbound Rules for AD Traffic

    Go to Network Security Groups (NSG) associated with the subnet or VMs.
    Add inbound rules for AD-related traffic:
        TCP/UDP Port 53 (DNS)
        TCP Port 135 (RPC)
        TCP Ports 389, 636 (LDAP/LDAPS)
        TCP Port 445 (SMB)
        TCP Ports 49152–65535 (Dynamic RPC)

c. Install Active Directory Domain Services Role

    RDP into the first VM (AD-DC1).
    Open Server Manager > Add Roles and Features.
    Select Active Directory Domain Services and click Next.
    Install the feature and reboot if required.

3. Promote First VM to Domain Controller

    Open Server Manager on AD-DC1.
    Click the notification flag and choose Promote this server to a domain controller.
    Choose Add a new forest and enter a root domain name (e.g., contoso.local).
    Set a Directory Services Restore Mode (DSRM) password.
    Complete the wizard and let the system reboot.

4. Configure Second Domain Controller for Redundancy

    RDP into the second VM (AD-DC2).
    Repeat the steps to install the Active Directory Domain Services role.
    Open Server Manager, click the notification flag, and choose Promote this server to a domain controller.
    Select Add a domain controller to an existing domain and enter the root domain (contoso.local).
    Use the admin credentials and complete the wizard.

5. Connect Employees and Administrators
a. Set Up a VPN for Secure Connectivity

    Deploy an Azure VPN Gateway to provide secure connectivity between on-premises users and the Azure virtual network.
    Configure the VPN Gateway with a Point-to-Site or Site-to-Site connection.
    Ensure client machines have access to the Azure virtual network.

b. Add Users and Groups

    Open Active Directory Users and Computers (ADUC) on AD-DC1.
    Create Organizational Units (OUs) for employees and administrators (e.g., OU=Employees, OU=Administrators).
    Add user accounts and assign them to the appropriate OUs.

c. Configure Group Policies

    Open the Group Policy Management Console (GPMC) on AD-DC1.
    Create and link GPOs to OUs for specific settings (e.g., password policies, drive mappings, software deployment).
    Test the policies on user accounts to ensure they apply correctly.

d. Join Workstations to the Domain

    On each workstation, open System Properties > Change Settings.
    Enter the domain name (contoso.local) and provide domain admin credentials.
    Reboot the workstations to apply domain settings.

6. Testing and Monitoring
a. Verify DNS Functionality

    Use the nslookup command on domain-joined machines to test DNS resolution.

b. Test Domain Logins

    Log in to domain-joined machines with domain user accounts to confirm functionality.

c. Monitor Domain Health

    Use Active Directory Administrative Tools and Event Viewer to monitor domain controller health and replication status.
    Run the dcdiag command to diagnose any potential issues.

7. Best Practices

    Backups: Regularly back up your AD data using Azure Backup or a similar tool.
    Redundancy: Always deploy at least two domain controllers for fault tolerance.
    Security: Restrict access to domain controllers and secure administrative accounts using multi-factor authentication (MFA).
    Monitoring: Use Azure Monitor to track VM and network performance.

By following these steps, you can deploy and manage an on-premises Active Directory environment hosted within Microsoft Azure, ensuring secure access for employees and administrators.
