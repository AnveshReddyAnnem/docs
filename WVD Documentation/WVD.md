## Requirements

You need an Azure account and subscription to quickly deploy and manage your virtualization environment. These are the Azure components that factor into the price of a Windows Virtual Desktop deployment.

- Virtual machines and operating system (OS) storage
- Data disk (personal desktop only)
- User profile storage
- Networking



Access Windows 10 Enterprise and Windows 7 Enterprise desktops and apps at no additional cost if you have an eligible Windows or Microsoft 365 license. Get free Extended Security Updates until January 2023 for your Windows 7 virtual desktop—offering more options to support legacy apps while you transition to Windows 10.

Access desktops powered by Windows Server Remote Desktop Services desktops and apps at no additional cost if you are an eligible Microsoft Remote Desktop Services (RDS) Client Access License (CAL) customer.

|                TYPE                 |                         DESCRIPTION                          |                         ELIGIBILITY                          |
| :---------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| Virtualize Windows 10 and Windows 7 | Access Windows 10 Enterprise and Windows 7 Enterprise desktops and apps at no additional cost if you have an eligible Windows or Microsoft 365 license. Get free Extended Security Updates until January 2023 for your Windows 7 virtual desktop—offering more options to support legacy apps while you transition to Windows 10. | You are eligible to access Windows 10 and Windows 7 with Windows Virtual Desktop if you have one of the following **per user** licenses: Microsoft 365 E3/E5, Microsoft 365 A3/A5/Student Use Benefits, Microsoft 365 F3, Microsoft 365 Business Premium, Windows 10 Enterprise E3/E5, Windows 10 Education A3/A5, Windows 10 VDA per user |
|      Virtualize Windows Server      | Access desktops powered by Windows Server Remote Desktop Services desktops and apps at no additional cost if you are an eligible Microsoft Remote Desktop Services (RDS) Client Access License (CAL) customer. | You are eligible to access Windows Server 2012 R2 and newer desktops and apps if you have a per-user or per-device RDS CAL license with active Software Assurance (SA). |



Your infrastructure needs the following things to support Windows Virtual Desktop:

- An [Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/).
- A Windows Server Active Directory in sync with Azure Active Directory. You can configure this using Azure AD Connect (for hybrid organizations) or Azure AD Domain Services (for hybrid or cloud organizations).
  - A Windows Server AD in sync with Azure Active Directory. User is sourced from Windows Server AD and the Windows Virtual Desktop VM is joined to Windows Server AD domain.
  - A Windows Server AD in sync with Azure Active Directory. User is sourced from Windows Server AD and the Windows Virtual Desktop VM is joined to Azure AD Domain Services domain.
  - A Azure AD Domain Services domain. User is sourced from Azure Active Directory, and the Windows Virtual Desktop VM is joined to Azure AD Domain Services domain.
- An Azure subscription, parented to the same Azure AD tenant, that contains a virtual network that either contains or is connected to the Windows Server Active Directory or Azure AD DS instance.



User requirements to connect to Windows Virtual Desktop:

- The user must be sourced from the same Active Directory that's connected to Azure AD. Windows Virtual Desktop does not support B2B or MSA accounts.
- The UPN you use to subscribe to Windows Virtual Desktop must exist in the Active Directory domain the VM is joined to.

The Azure virtual machines you create for Windows Virtual Desktop must be:

- [Standard domain-joined](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/compare-identity-solutions) or [Hybrid AD-joined](https://docs.microsoft.com/en-us/azure/active-directory/devices/hybrid-azuread-join-plan). Virtual machines can't be Azure AD-joined.
- Running one of the following [supported OS images](https://docs.microsoft.com/en-us/azure/virtual-desktop/overview#supported-virtual-machine-os-images).



For a list of URLs you should unblock for your Windows Virtual Desktop deployment to work as intended, see our [Safe URL list](https://docs.microsoft.com/en-us/azure/virtual-desktop/safe-url-list).



For optimal performance, make sure your network meets the following requirements:

- Round-trip (RTT) latency from the client's network to the Azure region where host pools have been deployed should be less than 150 ms. Use the [Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment) to view your connection health and recommended Azure region.
- Network traffic may flow outside country/region borders when VMs that host desktops and apps connect to the management service.
- To optimize for network performance, we recommend that the session host's VMs are collocated in the same Azure region as the management service.

## Architecture

![](.\images\windows-virtual-desktop.png)

#### Components Microsoft manages

Microsoft manages the following Windows Virtual Desktop services as part of Azure:

- **Web Access:** The [Web Access](https://docs.microsoft.com/en-us/azure/virtual-desktop/connect-web) service within Window Virtual Desktop lets users access virtual desktops and remote apps through an HTML5-compatible web browser as they would with a local PC, from anywhere on any device. You can secure Web Access using multifactor authentication in Azure Active Directory.
- **Gateway:** The Remote Connection Gateway service connects remote users to Windows Virtual Desktop apps and desktops from any internet-connected device that can run a Windows Virtual Desktop client. The client connects to a gateway, which then orchestrates a connection from a VM back to the same gateway.
- **Connection Broker:** The Connection Broker service manages user connections to virtual desktops and remote apps. The Connection Broker provides load balancing and reconnection to existing sessions.
- **Diagnostics**: Remote Desktop Diagnostics is an event-based aggregator that marks each user or administrator action on the Windows Virtual Desktop deployment as a success or failure. Administrators can query the event aggregation to identify failing components.
- **Extensibility components**: Windows Virtual Desktop includes several extensibility components. You can manage Windows Virtual Desktop using Windows PowerShell or with the provided REST APIs, which also enable support from third-party tools.

### Components you manage

Customers manage these components of Windows Virtual Desktop solutions:

- **Azure Virtual Network:** [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) lets Azure resources like VMs communicate privately with each other and with the internet. By connecting Windows Virtual Desktop host pools to an Active Directory domain, you can define network topology to access virtual desktops and virtual apps from the intranet or internet, based on organizational policy. You can connect a Windows Virtual Desktop to an on-premises network using a virtual private network (VPN), or use [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) to extend the on-premises network into the Azure cloud over a private connection.

- **Azure AD:** Windows Virtual Desktop uses [Azure AD](https://azure.microsoft.com/services/active-directory/) for identity and access management. Azure AD integration applies Azure AD security features like conditional access, multi-factor authentication, and the [Intelligent Security Graph](https://www.microsoft.com/security/business/intelligence), and helps maintain app compatibility in domain-joined VMs.

- **AD DS:** Windows Virtual Desktop VMs must domain-join an [AD DS](https://azure.microsoft.com/services/active-directory-ds/) service, and the AD DS must be in sync with Azure AD to associate users between the two services. You can use [Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-azure-ad-connect) to associate AD DS with Azure AD.

- **Windows Virtual Desktop session hosts:** A host pool can run the following operating systems:

  - Windows 7 Enterprise
  - Windows 10 Enterprise
  - Windows 10 Enterprise Multi-session
  - Windows Server 2012 R2 and above
  - Custom Windows system images with pre-loaded apps, group policies, or other customizations

  You can choose VM sizes, including GPU-enabled VMs. Each session host has a Windows Virtual Desktop host agent, which registers the VM as part of the Windows Virtual Desktop workspace or tenant. Each host pool can have one or more app groups, which are collections of remote applications or desktop sessions that users can access.

- **Windows Virtual Desktop workspace:** The Windows Virtual Desktop workspace or tenant is a management construct to manage and publish host pool resources.

### Personal and pooled desktops

Personal desktop solutions, sometimes called persistent desktops, allow users to always connect to the same specific session host. Users can typically modify their desktop experience to meet personal preferences, and save files in the desktop environment. Personal desktop solutions:

- Let users customize their desktop environment, including user-installed applications and saving files within the desktop environment.
- Allow assigning dedicated resources to a specific user, which can be helpful for some manufacturing or development use cases.

Pooled desktop solutions, also called non-persistent desktops, assign users to whichever session host is currently available, depending on the load-balancing algorithm. Because the users don't always return to the same session host each time they connect, they have limited ability to customize the desktop environment and don't usually have administrator access.

## Supported virtual machine OS images

Windows Virtual Desktop supports the following x64 operating system images:

- Windows 10 Enterprise multi-session, version 1809 or later
- Windows 10 Enterprise, version 1809 or later
- Windows 7 Enterprise
- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2

Windows Virtual Desktop does not support x86 (32-bit), Windows 10 Enterprise N, or Windows 10 Enterprise KN operating system images. Windows 7 also doesn't support any VHD or VHDX-based profile solutions hosted on managed Azure Storage due to a sector size limitation.

Available automation and deployment options depend on which OS and version you choose, as shown in the following table:

| Operating system                                    | Azure Image Gallery | Manual VM deployment | Azure Resource Manager template integration | Provision host pools on Azure Marketplace |
| :-------------------------------------------------- | :-----------------: | :------------------: | :-----------------------------------------: | :---------------------------------------: |
| Windows 10 Enterprise (multi-session), version 2004 |         Yes         |         Yes          |                     Yes                     |                    Yes                    |
| Windows 10 Enterprise (multi-session), version 1909 |         Yes         |         Yes          |                     Yes                     |                    Yes                    |
| Windows 10 Enterprise (multi-session), version 1903 |         Yes         |         Yes          |                     No                      |                    No                     |
| Windows 10 Enterprise (multi-session), version 1809 |         Yes         |         Yes          |                     No                      |                    No                     |
| Windows 7 Enterprise                                |         Yes         |         Yes          |                     No                      |                    No                     |
| Windows Server 2019                                 |         Yes         |         Yes          |                     No                      |                    No                     |
| Windows Server 2016                                 |         Yes         |         Yes          |                     Yes                     |                    Yes                    |
| Windows Server 2012 R2                              |         Yes         |         Yes          |                     No                      |                    No                     |



## Exercises

#### Deploying WVD Components

- Create a tenant in WVD - [Link](https://docs.microsoft.com/en-us/azure/virtual-desktop/virtual-desktop-fall-2019/tenant-setup-azure-active-directory)
- Create a  Host Pool - [Link](https://docs.microsoft.com/en-us/azure/virtual-desktop/create-host-pools-azure-marketplace)
- Create Desktop and RemoteApp groups - [Link](https://docs.microsoft.com/en-us/azure/virtual-desktop/manage-app-groups)
- Customize RemoteApp and Desktop Feed

#### Managing WVD Deployment

- Deploying a management tool
- Configure WVD Load Balancing method
- Configure Dynamic scaling of Session Hosts
- Manage Host Pool Service Updates
- Customize service principles and role Assignments

#### Customize WVD Images

- Understand FSLogix User Profile Solution
- Prepare FSLogix User Profile Solution
- Customize a VM image for WVD
- Deploying a Host Pool using ARM templates or Terraform

