![image](https://github.com/user-attachments/assets/f6ca32bd-6450-43ba-a30c-c05933442b2a)

<p align="center">
<img src="https://github.com/user-attachments/assets/58dcf0d9-b808-458d-b937-6dc44d6a7bf7" alt="image" />
</p>


<h1>Network Security Groups (NSGs) and Inspecting Network Protocols</h1>
A 🔐security-focused project demonstrating the configuration and analysis of Azure Network Security Groups (NSGs) and the inspection of network protocols to enforce access controls and monitor network traffic.
<br>
Feel free to try it for yourself at no cost since <a href="https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account">Microsoft Azure</a> can be used with a free subscription for 30 days and/or $200 worth of credits. Lets do it!

<br><br>

🛡️**Network Security Groups (NSGs)** → These are core components of cloud security (especially in Azure). They control inbound/outbound traffic at the subnet or NIC level — this is fundamental to network access control and segmentation.

👀**Inspecting Network Protocols** → This involves analyzing how data flows over the network, identifying potential vulnerabilities, misconfigurations, or unusual traffic patterns. This is classic security ops / defensive security / monitoring territory.

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
  <img src="https://github.com/user-attachments/assets/18f3d3ba-29d4-4a97-b8a3-f89e9e82eeb1" width="800" />
</div>

<br><br>

- Once you get logged into WatchDog1 (Windows 10 VM), pull up a web browser and deny all the options presented to you (no big deal if you accept any)
- Within the VM, navigate to https://www.wireshark.org and Install Wireshark
- Click "Download" and select "Windows x64 Installer" -> Click "Open file" once download is complete in the top right
- Click "Next" in the Installation Wizard -> "Noted" -> Keep clicking "Next" until it installs ***Keep a lookout for Npcap Setup window to pop up. Mine popped up behind the Wireshark installation window so look out for that.***
- Click "I Agree" on the Npcap Setup window -> "Install" (Don't worry about checking any of the boxes) -> "Finish" ***Wireshark installation should automatically continue after this.***

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/d2979427-5b5d-4545-aef0-0029e37c0f2b" width="500" />
  <img src="https://github.com/user-attachments/assets/be4e92fd-720a-46a1-8f7d-6da94dd3b68a" width="500" />
  <img src="https://github.com/user-attachments/assets/16a9f39a-058c-4a85-9c18-4b9fd24896f3" width="500" />
  <img src="https://github.com/user-attachments/assets/39260e2d-5fc0-4155-9307-ba40ea7c584c" width="500" />
</div>

<br><br>

- Click "Next" -> "Finish" in the Wireshark Installation Wizard
- Pull up Wireshark by Searching for it in the search text box on the taskbar

<br><br>

![image](https://github.com/user-attachments/assets/b0e70fc5-755d-4846-819b-77d484bf9687)

<br><br>

- Click the little blue fin in the top left to start the packet capture
- Type "ICMP" in the text box right below that and hit the "Enter" key to filter it to ICMP traffic

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/1d73fc08-e1aa-451f-b417-094fe6a9ac0a" width="600" />
  <img src="https://github.com/user-attachments/assets/61b3b7fa-0e7f-47a7-bb41-6b5f99475bf3" width="600" />
</div>

<br><br>

***We need to grab the priate IP Address of Linux-VM so we can ping it from within WatchDog1***

<br><br>

- From the Azure VM screen, click on the Linux-VM
- Scroll down to the "Networking" portion of the info
- Take note of the Private IP address
- Back in the Windows 10 VM (WatchDog1), pull up PowerShell by searching it in the search box on the taskbar
- Type "ping (Linux-VM's private Ip address)" and hit Enter, for example mine would be: ping 10.0.0.5
- observe the packets in Wireshark

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/32fcfe2e-7118-4d23-bcf4-a8ef75e4aaa0" width="500" />
  <img src="https://github.com/user-attachments/assets/9d8c1e4a-932a-4d14-95a1-b4a8114b07a2" width="500" />
</div>

<br><br>

![image](https://github.com/user-attachments/assets/c5e6f5ef-d2f7-43d6-99f4-9fabf06f5c4b)

<br><br>

***Notice the request and the replies in Wireshark. You can click on either one and inspect the packet details towards the bottom section which will show the MAC address and IP address (Data Link Layer/Network Layer) it came from or was sent to depending on if its the reply/request you are looking at. You can also view the payload of the packet, time of arrival/package was sent, and more.*** 

- In PowerShell ype "ping (Linux-VM's private Ip address) -t" and hit Enter to send a continuous ping and observe the traffic in Wireshark
- Hit Ctrl + c to stop the activity
- Type "ping google.com" and observe the changes

<br><br>

![image](https://github.com/user-attachments/assets/453086f3-c5e8-4a27-a93f-a6cc4f6e1db6)
<br>
![image](https://github.com/user-attachments/assets/f21d5b3c-1792-413a-9daf-cd2ac979f0d2)

<br><br>

- Try putting that IP address for google.com in a web browser
- Mine took me to Google.com 😎

<br><br>

![image](https://github.com/user-attachments/assets/792e23ef-dd5e-4529-9170-da2e38f6d413)

<br><br>

***You can also copy and paste or type any of the packet details from Wireshark in ChatGPT and it'll break it down for you🆒🆒🆒! Gotta love A.I. right?*** 😂

<br><br>

![image](https://github.com/user-attachments/assets/d07b1f47-0905-4915-b3ee-625234fb79e0)
![image](https://github.com/user-attachments/assets/e4eecac5-73b2-473b-ba21-78c97c7b845d)
![image](https://github.com/user-attachments/assets/c91d3629-05bd-47f6-80f8-a78525584d71)

<br><br>

***Ok back to what we came here to do. Next, we'll configure a 🛡️🧱Firewall (Network Security Group) in Azure and observe it doing its thing.***

<br><br>

- In the Windows 10 VM (WatchDog1), start a non-stop ping to the Linux-VM (Ubuntu Server) ***If you need to see how to do this again, please scroll up a bit to where we intitiated the non-stop ping***😁
- In the VM Screen in Azure, open up the info for Linux-VM by clicking on "Linux-VM"
- Navigate to Networking on the left side panel of the info window -> Click "Network Settings"
- Scroll down on the right side to "Rules" and click on the "+ Create a port rule" (Upper right of "Rules" in blue) drop down
- Click "Inbound Port Rule"

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/97fb03d7-32f2-499f-b730-597236029066" width="500" />
  <img src="https://github.com/user-attachments/assets/ccfd79f8-79d9-46cb-a34b-1a11556156d0" width="500" />
</div>

<br><br>

- Under "Protocol" select "ICMPv4"
- Under "Action" select "Deny"
- Under "Priority*" type "290" in the text box
- Click "Add"

<br><br>

![image](https://github.com/user-attachments/assets/a8a38761-4be5-4f71-a555-0c43e752bc03)

<br><br>

***Take a look at the firewall you just created.***

<br><br>

![image](https://github.com/user-attachments/assets/a1e103eb-ef3e-4b95-b602-eb52fa6e0e21)

<br><br>

- Go back into WatchDog1 and observe the traffic in Wireshark now (you should still have it filered to ICMP)
- Also note in PowerShell you will see the ping failing
<br>
***This is our NSG/Firewall in action. We put a block on incoming ICMP requests to the Linux-VM and this is what it looks like when we are pinging from WatchDog1. Notice how there are 0 replies being received from Linux-VM now.***
<br><br>

![image](https://github.com/user-attachments/assets/9af08348-60a2-49f2-be18-9ba0c06450d8)

<br><br>

- Go ahead and delete the NSG back in Azure -> Linux-VM -> Network Settings -> Scroll to the right to reveal the Trash Symbol to click on and delete it

<br><br>

![image](https://github.com/user-attachments/assets/1344c8c4-a298-4e9b-84d6-60908701e8bb)

<br><br>

- The pinging should start working normally again
- Hit "Ctrl + C" to stop the non-stop ping
<br><br>

***Now, we will Observe Secure Shell (SSH) Traffic. We'll be logging into the Linux-VM from WatchDog1's PowerShell through SSH protocol using Linux-VM's private IP address.***

<br><br>

- In WatchDog1, type "SSH" or "tcp.port==22" in the text box to filter the packet capture for SSH traffic only
- In PowerShell, type "ssh labuser@(Linux-VM's private IP address)" and hit Enter ***(This is case sensitive as well.)***
- It will have you type yes and the password for Linux-VM (***You will not be able to see the password as you type it in PowerShell so don't let that confuse you.***)
- Once successful, observe the traffic spam in WireShark (***Notice anything you type in will show traffic in WireShark.***)
- Type "exit" and hit Enter to exit the SSH connection

<br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/9b82bafb-8202-4437-8c97-340adcdf18f5" width="800" />
  <br>
  <img src="https://github.com/user-attachments/assets/73bd4314-fc57-414a-bf5c-6008a42fa0c7" width="800" />
</div>

<br><br>

***Lets Observe DHCP Traffic as well.***

- In WireShark, type "DHCP" or "udp.port==67 || udp.port==68" into the text box to filter for DHCP traffic
- Pull up NotePad by searching for it in the taskbar
- Type "ipconfig /release" -> hit Enter -> type "ipconfig /renew"
- Click File -> Save as -> type "c:\programdata" at the top -> in File name at the bottom type "dhcp.bat" and click "Save"
- In PowerShell type "cd c:\programdata" and hit Enter -> then type "ls" and hit Enter to see the file we just created
- Run dhcp.bat by typing ".\dhcp.bat and hit Enter (***Your connection will be lost and will pull back up on its own, so just give it a moment for that to happen.***)😉 
<br>
***The traffic will show the IP address releasing and renewing. It will show the entire handshake process for releasing and acquiring an IP address from the DHCP Server.***

  <br><br>

<div style="margin: 30px 0; text-align: center;">
  <img src="https://github.com/user-attachments/assets/f4f771ee-8530-4d81-980c-7a94b85cf8e7" width="500" />
  <img src="https://github.com/user-attachments/assets/a63f6684-0424-4edd-8b59-51c663d32e90" width="500" />
  <img src="https://github.com/user-attachments/assets/060904ce-08fc-4155-858e-b55d08de53dd" width="500" />
  <img src="https://github.com/user-attachments/assets/45789f2e-1fbd-466f-b3fa-be6883ff21e0" width="500" />
</div>

<br><br>

![image](https://github.com/user-attachments/assets/3d0d74f8-d183-42fb-96ea-778045d7e987)

<br><br>

***We'll review two more packet captures filtered for DNS Traffic and RDP Traffic before calling it the end of this lab. Thanks for sticking with me this far!*** 💪

- In Wireshark, filter the traffic to only show DNS traffic by typing "DNS" or "tcp.port==53 || udp.port==53"
- In PowerShell, (***First type "cd ~" to get back to the user directory/home directory if your still in c:\programdata or you could just close out PowerShell and reopen it.)*** type "nslookup google.com" (or "nslookup disney.com" or any website whom's IP address you'd like to look up)
- Observe the DNS Traffic in Wireshark
(***Feel free to copy/paste or type in any of the IP addresses you get for the websites you use with nslookup into a web browser to see if it takes you to their site. It may or may not.***)

<br><br>

![image](https://github.com/user-attachments/assets/f6563e62-8a33-44cd-bcd4-6a5793837a37)

<br><br>

- Now filter the traffic in Wireshark to show Remote Desktop Protocol (RDP) by typing "RDP" or "tcp.port==3389" in the text box
- Observe the traffic
(***It should be flowing non-stop due to us being logged into WatchDog1 using RDC, therefore its showing you a live stream connection from one computer to another, which traffic is always being transmitted between through the connection.***)

<br><br>

![image](https://github.com/user-attachments/assets/aeecac91-6efa-4f4d-8962-dc45c06df496)

<br><br>

***Well, that concludes this project! If you're done with playing around with the VMs and Wireshark, don't forget to delete the resource group we created so you don't rack up any charges! Or if you want to step away and come back to do some more or redo the entire project, check the boxes in the VM screen in Azure and click "Stop" to shut them down. That way you can come back and start them back up : ).***
<br>
***I hope you enjoyed it as much as I did and that it helped teach you a thing or two. Stay tuned for more upcoming Security projects from yours truly.*** 🙏😌
