<h1>Azure Sentinel VM Honeypot SIEM Map Lab</h1>

 
<h2>Description</h2>
In this lab we will create a VM in MS Azure, and log traffic trying to bruteforce an RDP login. We will then display the traffic geographically on a map using Sentinel. 


<h2>Environment used:</h2>

- <b>MS Azure</b>

<h2>Pre-requsites:</h2>
Note that you must have an MS Azure account to complete this lab. You can sign up for free and claim $200 worth of free credits here:

https://azure.microsoft.com/en-us/pricing/offers/ms-azr-0044p

<h2>Walk-through:</h2>

<p align="center">
<br />
<br />
<h2>Create a VM honeypot in Azure</h2>  <br/>
In Azure, search for VM.
  Select VM in the services drop down menu.
  
  On the VM page, click create.
  
  *Note: We want everything in this lab to be in the same resource group. Create a new resource group and name it anything you would like.
 
  Match your setting, to mine shown below. Name it whatever you want. You can change the region as needed, because the size you want may not be avaialable at the moment.
 
  ![1Create VM honeypot](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/eb7f4a74-f255-4c99-bc76-f5527345880f)


Make any username as password you'd like, just make sure to remember it or write it down somewhere. 
![2 set up account](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/83ad8548-bee0-4055-9056-e718077c47ec)

Make sure you allow RDP connections. (Port 3389)

On the Disks page, just go next to networking.

On the Networking Tab, go to NIC network security group and set it to advanced. 

Right below it, in the Configure network security section, click create new.

We will be opening the firewall so all public traffic can come in.

Remove the default rule, and select +Add an inbound rule. We're going to allow everything into the VM.

Under destination port, put a * to indicate all ports.

In the protocol setting, choose all.

Under Action, select allow.

Set the priority to 100.

Name it whatever you'd like. Take a look below for my settings.

![3Aloow all traffic](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/b484e29d-f053-4388-a039-7e5fb3d4ad1b)

We've now allowed ALL traffic from the internet into our virtual machine. 

Select Ok and then click the blue Review and Create button.

It should look similar to this.

![4Confirm n create](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/6f1aa033-692c-4ddf-9235-7e7da7ee1c32)

Select Create and then our virtual machine should start deploying. (Note this may take 10-15 minutes).

While waiting for the VM to deploy, we will create a log analytics workspace.

<h2>Create log analytics workspace</h2>  <br/>





<br />
<br />
Enter the number of passes: <br/>
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Confirm your selection:  <br/>
<img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Wait for process to complete (may take some time):  <br/>
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Sanitization complete:  <br/>
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Observe the wiped disk:  <br/>
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
