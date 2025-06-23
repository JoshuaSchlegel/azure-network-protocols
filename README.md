![image](https://github.com/user-attachments/assets/f6ca32bd-6450-43ba-a30c-c05933442b2a)

<p align="center">
<img src="https://github.com/user-attachments/assets/58dcf0d9-b808-458d-b937-6dc44d6a7bf7" alt="image" />
</p>


<h1>Network Security Groups (NSGs) and Inspecting Network Protocols</h1>
A security-focused project demonstrating the configuration and analysis of Azure Network Security Groups (NSGs) and the inspection of network protocols to enforce access controls and monitor network traffic. 👀
Feel free to try it for yourself at no cost since <a href="https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account">Microsoft Azure</a> can be used with a free subscription for 30 days and/or $200 worth of credits. Lets do it!

- **Network Security Groups (NSGs)** → These are core components of cloud security (especially in Azure). They control inbound/outbound traffic at the subnet or NIC level — that's fundamental to network access control and segmentation.

- **Inspecting Network Protocols** → This involves analyzing how data flows over the network, identifying potential vulnerabilities, misconfigurations, or unusual traffic patterns. That’s classic security ops / defensive security / monitoring territory.

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
- For "Size", select Standard_D2s_v3 - 2 vcpus, 8 GiB memory".
- I'll use the username: labuser and password: Password1234 for this VM. Make sure you check the "Licensing" box.

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/d539c131-6ba2-4783-b377-2eff77bbfdf3" width="500" />
  <img src="https://github.com/user-attachments/assets/57e26868-3430-4b49-8676-17297750c933" width="500" />
  <img src="https://github.com/user-attachments/assets/36f08826-7be6-46ac-9340-66525ae7da09" width="500" />
  <img src="https://github.com/user-attachments/assets/fb8cfaec-9935-4258-88b4-bf4584ca598a" width="500" />
</div>

<br><br>

- While creating the VM, allow it to create a new Virtual Network (Vnet) and Subnet by clicking on "
