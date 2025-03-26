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

-  Create two VM's
-  Ensure Connectivity between the client and Domain Controller
-  Install Active Directory
-  Join Client-1 to your domain 
-  Setup Remote Desktop for non-administrative users on Client-1
-  create additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>

- Create a resource group and virtual network
- Create the Domain Controller VM (Windows Server 2022) name it “Dc-1”:
<p>

![image](https://github.com/user-attachments/assets/386c6b2d-4446-49cf-887b-05dcdcbcad66)

<p>
  
![image](https://github.com/user-attachments/assets/a3fee9ee-eba7-4a91-91cc-5c016270d6f7)

</p>
<p>

Next create the second VM on (Windows 10) name it “Client-1”. Use the same Resource Group and Vnet that was created in previous step:

![image](https://github.com/user-attachments/assets/1e9bb43b-13a4-43b9-8542-9ab359531e23)


</p>
<br />

Set the controllers NIC Private IP address to static.
- Navigate to your DC-1 VM in Azure
- Navigate to "Network Settings"
- Click on "Network Interface / IP configuration" box
- Click on "ipconfig1"
- Under "Allocation", select "Static"
- Click "Save" 
 (this ensures DC-1's IP address will not change) --> check the NIC settings to make sure both VMs are on the same "Vnet". This will ensure both VMs can communicate & connect with each other later in this lab.

<p>
  
![image](https://github.com/user-attachments/assets/bc98fd32-622d-4824-80f9-9d24acf3a74c)

</p>
<p>

Connect to the Domain Controller with Remote Desktop

- Retrieve and copy public IP address of DC-1 VM
- Paste public IP address into "Computer" section of Remote Desktop and connect to the VM

Disable the firewalls in the Domain controller
- Within the DC-1 VM, navigate to "Windows Defender Firewall with Advanced Security"
- Click "Windows Defender Firewall Properties"
- Turn off "Firewall State" in "Domain Profile", "Private Profile", and "Public Profile" tabs
- Click "Apply" and "Ok"
- We are using Remote Desktop to view our domain controller and turn off the firewall because we need Client-1 to be able to access the DC-1 (domain controller) DNS server. 
![image](https://github.com/user-attachments/assets/48c2ff2a-15cd-47fa-8354-35142926562e)


</p>
<br />

Connect Client VM to Domain Controller VM

- Retrieve Dc-1 VM private IP address and copy it
- Navigate to Client-1 VM -> Network Settings -> click on "Network Interface / IP configuration" box
- On the left side of window, click on "DNS servers"
- Under "DNS servers", select the "Custom" option and paste the DC-1 private IP address
<p>
  
![image](https://github.com/user-attachments/assets/83331ce5-bf85-4d53-8fd8-dd02f00808c2)

- Click "Save"
- Navigate to "Virtual Machines" in Azure
- Select "Client-1" box
- Click "Restart", (we are restarting the VM to make sure the changes have been processed). 


Now, we can log in and Remote Desktop into Client-1 and open PowerShell from the Start menu. From there, we can ping the domain controller’s private IP and see if a connection is able to be established. Ping the Domain Controller by typing "ping(private IP address)" (Example: ping 10.0.0.4). After pinging Dc-1, if we see that the packets were able to be sent and received, then we can determine that a stable connection was established. We can type ipconfig /all in the command window, and it should show Dc-1’s private IP address under DNS server. 

![image](https://github.com/user-attachments/assets/ea32cd17-b1a9-4aeb-9f5f-530ca2ac78e1)

</p>
<p>

<h2>Install Active Directory</h2>  

- Connect to your DC-1 VM via Remote Desktop
- Open Server Manager Application
- Click "Add Roles and Features"
- Click "Next" until you reach "Server Roles"
- Select "Active Directory Domain Services" and click "Add Features"
- Click "Next" until you reach "Confirmation"
- Check box that says "Restart the destination server automatically if required"
- Click "Install" to complete installation


<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>



  
</p>
<br />


- Now in the Server Manager Dashboard, click on the flag at top right and click "Promote this server to a domain controller"
- Select "Add a new forest" option
- Set "Root domain name:" to "mydomain.com"
<p>

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

- Under "DNS options", uncheck "Create DNS delegation"
- Complete forest installation and your VM should automatically restart once installation is finished
- Log back into DC-1 VM as a domain user by typing "mydomain.com(your username)" as your username (Example: mydomain.com\labuser)

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

 Create a Domain Admin User inside of the domain
- Inside the  DC-1 VM, open Active Directory Users and Computers application
- Right click "mydomain.com" on the right side of window

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

- Click "New" then click "Organizational Unit"
- Fill in the name as "_EMPLOYEES" and click "Ok"
- Create another organizational unit
- Fill in the name as "_ADMINS"
- Click on the "_ADMINS" folder
- Inside the empty field on the right, right click -> click "New" -> then click "User"

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

- Fill out fields for a new user named "Jane Doe" with username "jane_admin"

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

- Proceed and finish user creation (NOTE: Feel free to uncheck "User must change password at next logon" box for your convenience)
- Next we will add jane_admin to the built-in "Domain Admins" Security Group
- Right click on "Jane Doe" and click "Properties"
- Navigate to "Member of" tab -> click "Add"
- Type "Domain Admins" in the empty field
- Click "Check Names" to confirm you found the correct object name, click "Ok", then click "Apply"

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

- Close or logout of the DC-1 VM connection
- Log back into DC-1 VM as "Jane Doe" (mydomain.com\jane_admin)
- From now on user "jane_admin" will be used as the admin account
  
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

Join client-1 to your domain
- Login to Client-1 VM as original local admin (in my case username = "labuser")
- Within the Client-1 VM, right click Windows start button -> then click "System"
- Click "Rename this PC (advanced)" on the right side of window

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

- Under "Computer Name" tab, click "Change
- Check "Domain" bubble, type "mydomain.com" in the field, then click "Ok"
  
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

- A "Windows Security" window should pop up
- Fill in the username and password with "Jane Doe" information (Username: mydomain.com\jane_admin)
- Click "Ok" and if done correctly, you will see the following window pop up

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

- Click "Ok" and your VM will restart
- Within DC-1 VM, open "Active Directory Users and Computers" application
- Expand "mydomain.com" -> then click on "Computers" to verify if Client-1 is there

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />  

Setup Remote Desktop for non-administrative users on Client-1
- Log into Client-1 VM as "Jane Doe" (mydomain.com\jane_admin)
- Navigate to "Settings" -> "System" -> "Remote Desktop"
- Click on "Select users that can remotely access this PC" at the bottom of window
- Click "Add"
- Type "Domain Users" in empty field
- Click "Check Names" to confirm you found the correct object name and click "Ok"
- Now all normal users within the domain can log into Client-1

<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />  
  

</p>
<br />

Create additional users and attempt to log into client-1 with one of the users


