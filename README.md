![image](https://github.com/user-attachments/assets/f6ca32bd-6450-43ba-a30c-c05933442b2a)

<p align="center">
<img src="https://github.com/user-attachments/assets/58dcf0d9-b808-458d-b937-6dc44d6a7bf7" alt="image" />
</p>


<h1>Network Security Groups (NSGs) and Inspecting Network Protocols</h1>
A 🔐security-focused project demonstrating the configuration and analysis of Azure Network Security Groups (NSGs) and the inspection of network protocols to enforce access controls and monitor network traffic.
<br>
Feel free to try it for yourself at no cost since <a href="https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account">Microsoft Azure</a> can be used with a free subscription for 30 days and/or $200 worth of credits. Lets do it!

<br><br>

🛡️**Network Security Groups (NSGs)** → These are core components of cloud security (especially in Azure). They control inbound/outbound traffic at the subnet or NIC level — that's fundamental to network access control and segmentation.

👀**Inspecting Network Protocols** → This involves analyzing how data flows over the network, identifying potential vulnerabilities, misconfigurations, or unusual traffic patterns. That’s classic security ops / defensive security / monitoring territory.

<h2>💻 Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute/Firewall)
- Remote Desktop
- Windows App (macOS)
- PowerShell
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>👨‍💻 Operating Systems Used </h2>

- Windows 11 Pro
- Windows 10 (21H2)
- Ubuntu Server 20.04
  
<h2>🪜 High-Level Steps</h2>

- Step 1: Create Virtual Machines and Connect to VMs using Remote Desktop Protocol (RDP) & Install Wireshark
- Step 2: Observe ICMP Traffic
- Step 3: Use NSG (Firewall) to Deny Ping
- Step 4: Observe SSH and DHCP Traffic
- Step 5: Observe DNS and RDP Traffic

<h2>🎬 Actions and Observations</h2>

***Assuming you have a Microsoft Azure account already setup, we will first need to create a Resource Group, a Virtual Machine (VM) using Windows 10, and a Linux Server VM.***

- In Microsoft Azure, type "resource" in the search bar at the top and click on "Resource groups"
- Click the "Create" button either towards top left or the blue one in the middle of the screen.
- I'll name it "SecurityLab" and click "Create + Review" -> then click "Create"
- You will then see a notification letting you know the Resource has been created successfully

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/9ba73821-0188-4eb4-9657-3d9a559dfccf" width="500" />
  <img src="https://github.com/user-attachments/assets/fe0a4491-e0e2-40e6-8c7b-6022c5f4f127" width="500" />
  <img src="https://github.com/user-attachments/assets/b2f7a1bb-e17b-4eb9-85aa-421c6f6cbaba" width="500" />
  <img src="https://github.com/user-attachments/assets/064b326b-b68b-456a-917e-21f3950dbec6" width="500" />
</div>

<br><br>

- From here, you can type "vm" into the search box up top and click on "Virtual machines"
- On the next screen you will click the "create" drop down in either the top left under "Virtual Machines" or the blue "create" drop down towards bottom middle -> "Azure Virtual Machine"
- Next, click on the "Resource group" drop down and select the resource group you created. I'll name the VM "WatchDog1" and keep it in the same region as the resource group.
- Don't worry about anything else other than the *Image and *Size.
- For the "Image" drop down, we will select "Windows 10 Pro, version 22H2 - x64 Gen2 (free services eligible)". ***(If you do not see this option immediately available, scroll all the way down within the drop down for "Image" and click on "See all images". The next screen will show you all available operating systems available and you can search or find "Windows 10", click the drop down and select "Windows 10 Pro, version 22H2 - x64 Gen2 (free services eligible)".***
- For "Size", select Standard_D2s_v3 - 2 vcpus, 8 GiB memory"
- I'll use the username: labuser and password: Password1234 for this VM. **Make sure you check the "Licensing" box**
- Click "Next : Disks >" and then "Next : Networking >"

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/d539c131-6ba2-4783-b377-2eff77bbfdf3" width="500" />
  <img src="https://github.com/user-attachments/assets/57e26868-3430-4b49-8676-17297750c933" width="500" />
  <img src="https://github.com/user-attachments/assets/36f08826-7be6-46ac-9340-66525ae7da09" width="500" />
  <img src="https://github.com/user-attachments/assets/fb8cfaec-9935-4258-88b4-bf4584ca598a" width="500" />
  <img src="https://github.com/user-attachments/assets/a3b675d7-8fd1-405f-93bf-2047826a5eac" width="500" />
  <img src="https://github.com/user-attachments/assets/7f3b4e40-d07b-47e2-bd54-1a53feb156d3" width="500" />
</div>

<br><br>

- While creating the VM, allow it to create a new Virtual Network (Vnet) and Subnet (**shouldn't have to click anything for this**)
- Click "Review + Create" on the bottom
- Click "Create" ***It will take a bit for the VM to be created so you can relax for a moment until the notification shows you that the VM has been deployed.***

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/b0d3adce-fd03-4e32-91da-b3a7a2332a89" width="500" />
  <img src="https://github.com/user-attachments/assets/95d18c62-44fe-466f-a687-927d39a8d06b" width="500" />
</div>

<br><br>

***Next, we will need to create a second VM. This time the *Image will be Linux (Ubuntu) Server and we'll need to make sure we create it inside the created Resource Group named "SecurityLab" and Virtual Network (VN)/Subnet.***
<br><br>

- As we did with the first VM creation, go to Virtual Machines in Azure and Create a new VM
- I'll name this VM "Linux-VM" and make sure its in the same region I created WatchDog1 in
- Select "Ubuntu Server 22.04 LTS - x64 Gen2" for the Image and Standard_D2s_v3 - 2vcpus, 8GiB memory" for the Size
- Select Password as the Authentication type
- To keep it simple, I'll use the same username and password as WatchDog1 - Username: labuser and Password: Password1234
- Click "Next : Disks >" and then click "Next : Networking >"
- Make sure the same VN and Subnet are the same as WatchDog1's -> Click "Review + Create" and then "Create"
- Wait for the VM to be Deployed

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/21bccedd-e50a-40f3-ae51-45339bcc482a" width="500" />
  <img src="https://github.com/user-attachments/assets/93c91d6d-d06e-41f0-a80d-a1c98ae6a270" width="500" />
  <img src="https://github.com/user-attachments/assets/a16c07a5-7bdf-4ce2-9701-b6edc4da1703" width="500" />
  <img src="https://github.com/user-attachments/assets/b02431a7-b387-4df2-9e3a-afebc83207ad" width="500" />
</div>

<br><br>

***Now we can use Remote Desktop to log in to our Windows 10 VM and observe ICMP Traffic. We will need to install Wireshark to capture the packets and observe the traffic, as you will see.***

- First, log into WatchDog1 by pulling up Remote Desktop (RDC) by typing "Remote Desktop Connection" in the Search bar on your taskbar
- In the Azure VM page, copy the public IP Adress located to the right of the VM and paste it into RDC
- Click Connect and use the username and password you created when you created the VM (labuser and Password1234) ***You may need to select "More Choices" -> "Use a different account"***
<br><br>
***When confronted with all the options for Windows 10 after logging into the VM and/or pulling up the Microsoft Edge web browser, you can decline/say no to everything. If you already clicked through with any of the options selected it is not a big deal.***

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/18f3d3ba-29d4-4a97-b8a3-f89e9e82eeb1" width="500" />
</div>

<br><br>





