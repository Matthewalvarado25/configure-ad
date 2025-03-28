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
-  Create additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>

- Create a resource group and virtual network
- Create the Domain Controller VM (Windows Server 2022) name it “Dc-1”:
<p>

![image](https://github.com/user-attachments/assets/386c6b2d-4446-49cf-887b-05dcdcbcad66)

<p>
  
![image](https://github.com/user-attachments/assets/a3fee9ee-eba7-4a91-91cc-5c016270d6f7)

</p>
<p>

- Next create the second VM on (Windows 10) name it “Client-1”. Use the same Resource Group and Vnet that was created in previous step:

![image](https://github.com/user-attachments/assets/1e9bb43b-13a4-43b9-8542-9ab359531e23)


</p>
<br />

Set the controllers NIC Private IP address to static.
- Go to your Dc-1 VM in Azure
- Go to "Network Settings"
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


Ensure Connectivity Between Domain Controller and Client

Login to the client-1 VM by copying the public IP address
Paste the public IP address into "Computer" section of Remote Desktop and connect to the VM
- Now, we can log in and Remote Desktop into Client-1 and open PowerShell from the Start menu.
- Ping the Domain Controller by typing "ping and entering the (private IP address)" (Example: ping 10.0.0.4)
- Oberse and make sure it works
  
 After pinging Dc-1, if we see that the packets were able to be sent and received, then we can determine that a stable connection was established. We can type ipconfig /all in the command window, and it should show Dc-1’s private IP address under DNS server. 

![image](https://github.com/user-attachments/assets/ea32cd17-b1a9-4aeb-9f5f-530ca2ac78e1)

</p>
<p>

<h2>Install Active Directory</h2>  

- Connect to your Dc-1 VM through Remote Desktop
- Open Server Manager App
- Click "Add Roles and Features"
- Click "Next" until you reach "Server Roles"
- Select "Active Directory Domain Services" and click "Add Features"
- Click "Next" until you reach "Confirmation"
- Check the box that says "Restart the destination server automatically if required"
- Click "Install" to complete installation


<p>
  
![image](https://github.com/user-attachments/assets/06e3f756-18fe-469b-a7de-1512b9a06ad4)

</p>
<p>



  
</p>
<br />

Promote to Domain Controller
- Now in the Server Manager, click on the flag at top right and click "Promote this server to a domain controller"
- Select "Add a new forest" 
- Set "Root domain name:" to "mydomain.com"
- Complete forest installation and your VM should automatically restart once installation is finished
<p>

![image](https://github.com/user-attachments/assets/fbfa5eb9-61ab-460c-81a9-b2f0df95239c)

</p>
<p>
</p>
<br />

- Log back into Dc-1 virtual machine as a domain user by typing "mydomain.com\(username)" as your username Ex: mydomain.com\labuser)


![image](https://github.com/user-attachments/assets/84f34088-7e18-4b7f-8551-120277a04e9c)

</p>
<p>
</p>
<br />


 Create a Domain Admin User inside of the domain
- Inside the Dc-1 virtual machine, click the start menu
- Click Windows Administrative Tools
- Click Active Directory Users and Computers application
- Right click "mydomain.com" on the left side of window



![image](https://github.com/user-attachments/assets/ce4ec7e8-1d19-44bc-adde-5142879df3f3)

</p>
<p>
</p>
<br />

- Click "New" then click "Organizational Unit"
- Fill in the name as "_EMPLOYEES" and click "Ok"
- Create another organizational unit
- Fill in the name as "_ADMINS"

![image](https://github.com/user-attachments/assets/deb16e78-8079-4963-b5d1-1699fbe15292)


- Click on the "_ADMINS" folder
- Inside the empty field on the right, right click -> click "New" -> then click "User"
- Fill out first and last name, and username "jane_admin" for a new user named "Jane Doe"
  
![image](https://github.com/user-attachments/assets/c644651a-9e92-4d5e-b604-dae52a0c6992)

</p>
<p>
</p>
<br />



</p>
<p>
</p>
<br />

- Next we will add jane_admin to the "Domain Admins" Security Group
- Right click on "Jane Doe" and click "Properties"
- Navigate to "Member of" tab -> click "Add"
- Type "Domain Admins" in the empty field
- Click "Check Names" to verify you found the correct group name, click "Ok", then click "Apply"

![image](https://github.com/user-attachments/assets/f8d46948-75c2-4a52-b3a7-9397b331dcbb)

</p>
<p>
</p>
<br />

- Logout of the Dc-1 VM connection
- Log back into Dc-1 VM as "Jane Doe" username: mydomain.com\jane_admin
- Use jane_admin as your admin account from now on
  

</p>
<p>
</p>
<br />

Join client-1 to your domain
- Login to Client-1 VM as original local admin (in my case username = "labuser")
- Within the Client-1 VM, right click Windows start button -> then click "System"
- Click "Rename this PC (advanced)" on the right side of window

![image](https://github.com/user-attachments/assets/907f1962-3a7e-4f19-98f7-73d8c9700a68)

</p>
<p>
</p>
<br />

- Under "Computer Name" tab, click "Change
- Check the cirlce before "Domain" , type "mydomain.com" in the text box, then click "Ok"
  
![image](https://github.com/user-attachments/assets/072469d8-6c81-42fc-99b9-67d558561f48)

</p>
<p>
</p>
<br />

- A "Windows Security" window should pop up
- Fill in the username and password with "Jane Doe" information (Username: mydomain.com\jane_admin)
- Click "Ok" and you will see the following window pop up

![image](https://github.com/user-attachments/assets/eefe9e79-8642-40f3-9252-36b61231c75b)


</p>
<p>
</p>
<br />

- Click "Ok" and your virtual machine will restart
- In the Dc-1 VM, open "Active Directory Users and Computers" app
- Extend "mydomain.com" -> then click on "Computers" to confirm that Client-1 is inside


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
- You can now log into Client-1 as a normal, non-administrative user now.

![image](https://github.com/user-attachments/assets/b7ea8256-06e9-4fb2-9abc-f3aa3acef5bd)

</p>
<p>
</p>
<br />  
  

</p>
<br />

Create additional users and attempt to log into client-1 with one of the users
- Login to Dc-1 as jane_admin using Remote Desktop 
- Open PowerShell_ise as an administrator.
- Create a new file and save the file to the desktop name it (Create-users)
- Copy and paste the contents of this [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)  into PowerShell ISE
- Click the green play button at the top to run the script
  
![image](https://github.com/user-attachments/assets/ff85286b-618c-4290-9174-cc42e397d65b)

</p>
<p>
</p>
<br />  



Run the script and observe the accounts being created

![image](https://github.com/user-attachments/assets/1a3d4cc5-1f11-4d67-acf8-2557ce85df36)

</p>
<p>
</p>
<br />  


Check to see in Active Directory that the users have been created 
- open "Active Directory Users and Computers" application
- Find the "_EMPLOYEES" folder
- Right click folder and select "Refresh" to see if the users have been created

![image](https://github.com/user-attachments/assets/db6b296e-7cfb-4dec-bda0-ea383ce6065e)


</p>
<p>
</p>
<br />  

Attempt to log into client-1 with one of the users
- Choose any new created user from the Active Directory 
- To make sure the script works, you can try to log in to Client-1 VM as the user you picked
- In this example the user that was chosen was "buw.gig" , the login username will be "mydomain.com\buw.gig"
- The password will be the same for all users, and can be found at the top of the script
  
  

![image](https://github.com/user-attachments/assets/cf88667d-12ab-477b-b6b4-0dd6f11f1a6b)

</p>
<p>
</p>
<br />  

![image](https://github.com/user-attachments/assets/3f0ceadd-49da-4f60-b6bb-1c8b5eafd03b)




Congrats on completing the assignment. Now that we're done, Don't forget to clean up your azure enviroment. Close your Remote Desktop connection, delete the Resource Group(s) created at the beginning of this tutorial, and verify Resource Group deletion.
