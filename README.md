<p align="center">
<img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/7ddd433c-a1ae-4f50-8075-20cd11ef33c0"/>
</p>

<h1>Active Directory on Azure Virtual Machines</h1>
Active Directory is a software built and maintained by Microsoft that:<br></br>

- Centrally manages thousands of user accounts in a single place (accounts, passwords, permission, etc.)
- Allows you to manage devices on a large scale
- Provides a way to control access to resources on a large scale

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Computers)
- Remote Desktop Protocol (RDP)
- Ping

<h2>Getting Started</h2>
First I'll set up 2 Virtual Machines, one will be the server that I'll install Active Directory on and promote to a Domain Controller. The other machine will be a client computer that we will add to the Domain. Inside Azure we need to set up a resource group that will hold both our VMs. You can get there by logging into Azure and search "Resource Group". 
<br></br>

<img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/b3344a59-ea45-4106-b0b2-6fe8de13fce0"/><br></br>

<h2>Virtual Machines</h2>
<p>
After the Resource Group is created you can search "Virtual Machines" and you'll see a similar screen. I'm going to create two machines, one will be Windows Server 2022 Data Center, and the other will be just a regular Windows 10 computer. Add both to the RG we just created. For this lab I'll be naming the Windows Server DC-1 and the Windows 10 computer Client-1. You want each to have a minimum of 2 vcpus (virtual cpu). Also make sure to put them

<img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/6b529d8b-a7b9-423c-a8f7-a52ccafe43ba"/>
<img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/93ed4b58-432c-48ba-a98a-315d2d921197"/>
</p>



<p>
The only thing left to do is to make sure that DC-1 and Client-1 are apart of the same Network. For this lab all the defaults are fine, just go to the Networking section and look at the name of the network and remember it. You can even create your own if you'd like and subnet it the way you want. But that is beyond the scope of this lab. After that hit "Review and Create".
</p>

<img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/e77195e2-99b6-4011-bb5b-dc70b44db035"/>

<h2>Tesing Connectivity</h2>

<p>
Now that both my VMs are set up, I'll remotely connect to them using RDP. Once connected, the very first thing we will do is test connectivity between the two machines. Grab DC1's private IP address by running ipconfig directly on the machine or you can see it back in the Azure portal by selecting the DC1 virutal machine and look under the Networking section. Now from Client-1 open cmd or Powershell and run ping -t. By default Windows only sends four ICMP request, the -t flag will send an unlimited number of pings until you stop it manually. I'm doing this because also by default Windows blocks these pings for security reasons and we need to go back to DC1 and open the Firewall and allow these pings to come through so that we can see connectivity.
</p>


<h4>"Request timed out" message</h4>

<img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/a8e9cb8c-d3b1-4869-8397-b3192e3332df"/>
</p>


<h4>DC1 Firewall</h4>

<p>
A quick way to get to the Firewall rules is to use the "run" utility. You can either right-click the Windows icon at the bottom of the screen and select "run" or you can use the keyboard shortcut (win + r). Once its open type wf.msc to open the rules and under Inbound Rules look for "Core Networking Diagnostics ICMP Echo" Profile = Private. Select it and Enable it and those ping request should be getting through now.
<br></br>

<img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/02f9ce2b-8e34-419f-986e-f69a40fe09a5"/>
</p>

<h4>Successful Ping</h4>

<p>
Back on Client-1 your command line should look like this<br></br>

<img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/c94a41e1-64bd-4ecc-beb1-4020e7763aa5"/>
</p>

<h2>Active Directory Deployment</h2>

<p>
Now that we're sure the computers can see each other over the network, we can now install Active Directory Domain Services. I'll do this by listing the Steps I take.

<ol>
  <li>Open Server Manager on DC1, from here select manage in the top-right of the screen. Click "Add Roles and Features.</li><br></br>
  <img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/55782ef8-a466-4e52-8a4f-95fa85dff6a1"/><br></br>
  
  <li>Click next on the "Before you Begin page".</li><br></br>
  
  <li>The Insallation type is "Role-based or feature-based" click next.</li><br></br>
  
  <li>DC1 should be the only server there to be selected. Click next.</li><br></br>
  <img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/72043d37-6854-4137-a923-25944b82b72b"/><br></br>
  
  <li>Select Active Directory Domain Services. Click next.</li><br></br>
  <img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/d419191c-9ee7-4220-ba68-4968f8f1f25f"/><br></br>
  
  <li>The next window is features tab and I just selected next. When we selected ADDS in the previous window it automatically added all the features we need in order to run Active Directory.</li><br></br>
  
  <li>Click next until you get to the Confirmation tab. Click install</li><br></br>
  <img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/43dfc822-b3ca-4b3c-8e9b-64bf655eb022"/><br></br>
  
  <li>After the installation is complete, you will see a yellow triangle at the top of the screen. Click it and select "Promote this server to a domain controller.</li><br></br>
  <img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/382333f8-5885-415e-b141-04f08b1dadc5"/><br></br>
  
  <li>I'm creating a brand new Domain so I'm selecting "Add a new forest and naming it testdomain.com</li><br></br>
  <img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/db2ff6d8-2c5b-4677-8c7b-eb55b17fb259"/><br></br>
  
  <li>Windows Server 2016 is fine and I wont be using the DSRM so just make up a password and click next </li><br></br>
  <img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/215c5752-8a75-4c78-8d1d-e1fa20e99824"/><br></br>

  <li>I clicked next on the DNS options since AD will install a DNS server automatically</li><br></br>
  
  <li>The Defaults are fine for this lab so I just hit next until I reach the Install tab</li><br></br>
  <img src="https://github.com/SpyderSec30/Active-Directory-within-Azure/assets/174487140/0881cb9c-9b39-4d7b-bb18-cd39d49e6c04"/><br></br>

  <li>After the installation is complete, the Computer will restart and our RDP connection will be lost. To sign into the new Active Directory Domain I used testdomain\labuser1.</li><br></br>

  <h4>We have now successfully installed Active Directory and promoted DC1 into a Domain Controller.</h4>
</ol>
</p>















