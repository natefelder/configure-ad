<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

# On-premises Active Directory Deployed in the Cloud (Azure)
Active Directory centrally manages thousands of user accounts in a single place (accounts, passwords and permissions) as well as manage devices on a large scale.
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

## Operating Systems Used

- Windows Server 2022
- Windows 10 (21H2)

## High-Level Deployment and Configuration Steps

- Domain Controller VM (Windows Server 2022) named “DC-1”
- Domain Controller’s NIC Private IP address to be static
- ICMPv4 (ping) was allowed on the Domain Controller
- Create an Admin and Normal User Account in Active Directory
- Join Client to domain
- Attempt to login Client-1 with one of the users

| Terms | Descriptions|
|-------| ------------|
| Resource Group |  a container that holds related resources for an Azure solution. The resource group can include all the resources for the solution, or only those resources that you want to manage as a group.
| Virtual Machine |  a digital version of a physical computer. Virtual machine software can run programs and operating systems, store data, connect to networks, and do other computing functions, and requires maintenance such as updates and system monitoring
| Remote Desktop | a client application that allows a “client” computer to connect to a “host” computer from a remote location. Users can then control and use the applications and files on the host device from anywhere.
| Active Directory Domain Services (AD DS) | a server role in Active Directory that allows admins to manage and store information about resources from a network, as well as application data, in a distributed database.
| Powershell |  is a cross-platform task automation solution made up of a command-line shell, a scripting language, and a configuration management framework. PowerShell runs on Windows, Linux, and macOS.
| Domain Controller (DC) |  are the servers in your network that host AD DS. DCs respond to authentication requests and store AD DS data
|Organizational Unit (OU) | a container within a Active Directory (AD) domain that can hold users, groups and computers. It is the smallest unit to which an administrator can assign Group Policy settings or account permissions.
|Join Client to Domain | a feature that lets users establish a remote and secure connection to a work domain using credentials from the enterprise, allowing them to effectively "join" that domain. |

## Deployment and Configuration Steps

First, we will need to establish the resource group so that you can add your virtual machines for the Domain Controller (DC-1) and the Client Virtual Machine (Client-1). The Domain Controller VM will use Windows Server 2022 system image (a serialized copy of the entire state of a computer system stored in some non-volatile form such as a file). 

<p align="center">
<img src="https://i.imgur.com/lKmRcIy.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
The Client VM (Windows 10) named “Client-1” was created with the same Resource Group and Vnet that was created in DC-1. 
<p align="center">
<img src="https://i.imgur.com/w34Z93S.png" height="80%" width="80%" alt="client 1 vm settings"/>
</p>
Private IP address set to static (static IP addresses are necessary to keep the same ip address.)
<p align="center">
<img src="https://i.imgur.com/n3KceWF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

Second, check for connection between the client device and domain controller by logging into "Client-1" with Remote Desktop Connection (RDP) and pinging DC-1’s private IP address using ping -t (perpetual ping). ICMPv4 (ping) was allowed on the Domain Controller's (DC-1) Firewall in Windows Firewall (Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)). After logging back into "Client-1" check to make sure the ping is successful.

<p align="center">
<img src="https://i.imgur.com/FWLTP8X.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

Image below displays that the icmp rule has been allowed on the windows firewall for inbound traffic:
<p align="center">
<img src="https://i.imgur.com/f4i0pdh.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>  
</p>
<br />  
While in DC-1, we've selected to 'add roles and features' to enable Active Directory Domain Services. Promoted as a Domain Controller (DC): a new forest as mydomain.com setup. Remote Desktop was Restarted and logged back into DC-1 as user: mydomain.com\labuser.

<p align="center">
<img src="https://i.imgur.com/ipCHp9t.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p align="center">
  <img src="https://i.imgur.com/Ccbt7ak.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

Next, we configure the organizational units for the admins and employees in Active Directory (AD) while continuing to be in DC-1 (Remote Desktop Connection).  The accounts can now be viewed in Active Directory in the appropriate organizational unit. In the Active Directory, right click on your <b>domain name</b> and move your mouse to hover over new --> "Organizational Unit" and left click to create folders for your AD. We will create employees, admins, and security groups.

<p align="center">
<img src="https://i.imgur.com/D59IbY9.png" height="80%" width="80%" alt="Active Directory OU"/>
  </p>
</br>
Create a new OU named '_ADMINS' --> Create a new employee named <b>Karen What</b> (same password) with the username of <b>'karen_admin'</b>. Once the admin is created, add "karen_admin" to the "domain admins" security group.

<p align="center">
<img src="https://i.imgur.com/Qzlpsyk.png" height="80%" width="80%" alt="Add user to domain admins"/>
  </p>
  </br>
Log out and close the connection to dc-1 for current user(mydomain.com\labuser) and log back in as "mydomain.com\karen_admin".
<p align="center">
<img src="https://i.imgur.com/WiQI5sG.png" height="80%" width="80%" alt="cmd displays new loggin user"/>
 </p>
  </br>
  
We'll join "Client-1" to the domain< (mydomain.com); however, we must change the DNS on Client-1 to the private IP address of "DC-1" so that we can properly add client-1 to the domain. Here we will select the NIC on client-1 to change the dns to the private IP address of DC-1
 
  <p align="center">
    <img src="https://i.imgur.com/f2Vka1D.png" height="80%" width="80%" alt="select network inferface client 1"/> </p>
    </br>
    Select 'DNS Servers'
      <p align="center">
    <img src="https://i.imgur.com/BlO0rjn.png" height="80%" width="80%" alt="select network inferface client 1"/> </p>
    </br>
    Select 'Custom' radio button for DNS server so you can now connect to DC-1 private IP address.
  <p align="center">
  <img src="https://i.imgur.com/Rcjik7d.png" height="80%" width="80%" alt="select customer dns"/></p>
  </br>
  Now that we have successfully changed the DNS server to the private IP address of DC-1, we can add client-1 to the domain without error. You will receive a message letting you know that the client has been successfully added to the domain. This can be done by going to System -> Rename This PC -> enter domain name -> select OK -> select Apply. The update this then require a system restart.  
  <p align="center">
    <img src="https://i.imgur.com/v2B6jza.png" height="80%" width="80%" alt="add to domain"/></p>
    </br>
    Message displays that client has been successfully add to the domain
    <p align="center">
  <img src="https://i.imgur.com/awUUK52.png" height="80%" width="80%" alt="message displays client added to domain"/>
  </p>
  Now, we can create our users that will be loaded into our <b>_EMPLOYEES OU</b> in the domain controller (DC-1). To create these employees we will run `PowerShell_ISE` as an <b>administrator</b>. A new File will be created then we can enter the pre-configured script into the file. When the script is run, the random employees will be created.
  
<details>  
  <summary> <h6>pre-configured Powershell Script</h6> </summary>
  
  
```powershell
# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$NUMBER_OF_ACCOUNTS_TO_CREATE = 10000
# ------------------------------------------------------ #

Function generate-random-name() {
    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
    $vowels = @('a','e','i','o','u','y')
    $nameLength = Get-Random -Minimum 3 -Maximum 7
    $count = 0
    $name = ""

    while ($count -lt $nameLength) {
        if ($($count % 2) -eq 0) {
            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
        }
        else {
            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
        }
        $count++
    }

    return $name

}

$count = 1
while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
    $fisrtName = generate-random-name
    $lastName = generate-random-name
    $username = $fisrtName + '.' + $lastName
    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $firstName `
               -Surname $lastName `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
    $count++
}
```
  
</details>

  Here is the script loaded into powershell prior to running the script to create 1000 random users
  <p align="center">
  <img src="https://i.imgur.com/ez4THWm.png" height="80%" width="80%" alt="powershell with script loaded"/>
  </p>
  </br>
  Random users are now created after choosing to execute the code. Here we can now see the script loading the 1000 users:
<p align="center">    
  <img src="https://i.imgur.com/f2vKx8Y.png" height="80%" width="80%" alt="powershell execute code"/> </p>
  Those random Users are now reflected in Active Directory on the Domain Controller under _EMPLOYEES
  <p align="center">
  <img src="https://i.imgur.com/lHBM2nh.png" height="80%" width="80%" alt="active directory shows created users"/>
  </p>
  Attempt to login on Client-1 with a random user that has been created
  <p align="center">
  <img src="https://i.imgur.com/HFguOhB.png" height="80%" width="80%" alt="windows start menu shows login user"/>
  </p>
  <p align="center"><b><i>"Congratulations on completing Active Directory lab.”</p></b></i>
<p align="center"><b><i></p></b></i>
<br />
<br />
<p align="right"> Next up, <a href=> Azure Network Protocols </a></p>
