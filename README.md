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

Now that you created the domain controller (Dc-1), set the controllers NIC Private IP address to static. Go to Dc-1's network settings --> select networking --> click the hyperlink under "network interface/ IP Configuration" --> "ipconfig1" --> change the IP address from dynamic to static (this ensures DC-1's IP address will not change) --> check the NIC settings to make sure both VMs are on the same "Vnet". This will ensure both VMs can communicate & connect with each other later in this lab.

<p>
  
![image](https://github.com/user-attachments/assets/bc98fd32-622d-4824-80f9-9d24acf3a74c)

</p>
<p>
Firstly, let's use Remote Desktop to view our domain controller and turn off the firewall because we need Client-1 to be able to access the DC-1 (domain controller) DNS server. 
- Within the DC-1 VM, navigate to "Windows Defender Firewall with Advanced Security"
- Click "Windows Defender Firewall Properties"
- Turn off "Firewall State" in "Domain Profile", "Private Profile", and "Public Profile" tabs
- Click "Apply" and "Ok"

![image](https://github.com/user-attachments/assets/48c2ff2a-15cd-47fa-8354-35142926562e)


</p>
<br />

After doing this, we need to change the DNS settings of Client-1 within Azure so that the virtual machine relies on the domain controller for DNS. In Microsoft Azure, we need to locate the private IP address of Dc-1, and then we go to the custom DNS server setting for Client-1 and change it to the private IP address we obtained which was 10.0.0.4 and click save. You may need to restart the Client-1 VM to make sure the DNS settings are active. 

<p>
  
![image](https://github.com/user-attachments/assets/83331ce5-bf85-4d53-8fd8-dd02f00808c2)




Now, we can log in and Remote Desktop into Client-1 and open PowerShell from the Start menu. From there, we can ping the domain controller’s private IP and see if a connection is able to be established. After pinging Dc-1, if we see that the packets were able to be sent and received, then we can determine that a stable connection was established. Lastly, we can type ipconfig /all in the command window, and it should show Dc-1’s private IP address under DNS server.


  
![image](https://github.com/user-attachments/assets/ea32cd17-b1a9-4aeb-9f5f-530ca2ac78e1)

</p>
<p>
  
Install Active Directory

  
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
