<h1>Azure Sentinel VM Honeypot SIEM Map Lab</h1>

 
<h2>Description</h2>
In this lab we will create a VM in MS Azure, and log traffic trying to bruteforce an RDP login. We will then display the traffic geographically on a map using Sentinel. 


<h2>Environment used:</h2>

- <b>MS Azure</b>
- <b>Windows 10</b>

<h2>Pre-requsites:</h2>
Note that you must have an MS Azure account to complete this lab. You can sign up for free and claim $200 worth of free credits here:

https://azure.microsoft.com/en-us/pricing/offers/ms-azr-0044p

<h2>Create a VM honeypot in Azure</h2> 
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

![5 Deployment in progress](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/88f58569-854e-463f-9525-d2244f19c7f1)

While waiting for the VM to deploy, we will create a log analytics workspace.

<h2>Create log analytics workspace and connect it to our VM</h2>  <br/>

We need to "ingest" logs from the VM (IE the windows event logs), so we will set up a workspace to do that. Later we will create our own custom log that contains geographical workspace. We will create the logs so our SIEM can connect to them and display the geodata on the map.

Now, in the searchbar look type "log analytics" and select log analytics workspace.

![6 make logs](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/cd64b40f-7d20-4fbe-b06b-77a0244f9fbe)

On this page select Create log analytics workspace.

Here, we will select the resource group we created when we set up the VM. In my case, it is named Honeypotlab.

You can name it whatever you would like. I named it law-HPL (short for log analytics workspace honeypot lab)

Make sure to match the region your VM was created in.

![7Create log rule](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/bfd57481-634e-40e6-9550-bcc6e6b6d2e9)

Select Reivew + Create, and then create.

Next, we will go to Microsoft Defender to enable the ability to gather logs.

Search "Defender for cloud" and select it from the services.

![8go to security center (defender for cloud)](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/a5552809-172b-43e0-b6b3-35591b44c37c)

Then, Navigate to Environment Settings.

![9 pricing and setting (now Environment settings)](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/edda17fe-ab18-4ee0-9cf4-386d3bef9aa5)

Here, select your log analytics workspace.

![10 Click log analytic workspace](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/c4e32886-308e-45f6-8da4-93b50a4607ef)

On this page, select enable all plans and save.

![11 enable all](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/0ae2c731-45f7-4af1-b423-cfa1546a26f6)

In the Data Collections page, select "All Events" and save.

![12 Log all events](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/b7b65a7f-819a-48a0-8f8c-dc40773ec730)

Now we're going to go back to our log analytics workspace and connect it to our VM.

![13 log anayltics](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/8fad4f0f-cf7b-4c0c-bdb5-a5c250da0cb0)

Select our workspace on the Log analytics workspace page.

![15 navigate to VM](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/5a63ff25-15f9-4006-9ac2-e6c81fab5ab7)

On the left, scroll down and find "Virtual Machines".

![16 VM](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/e52c4d63-25e0-44b3-8df6-6d98f2d50549)

Select our VM and then on the next page select "Connect". This may take some time.

![17 click connec](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/69017009-9b82-4723-8993-97ed3d32a5d5)


<h2>Setting up our SIEM (Sentinel)</h2> 
In the search bar search for "Sentinel" and select Microsoft Sentinel.

![18 add sentinel](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/7b5435cf-d8ca-4c6f-9653-2a430b24b9a8)

Select Create Microsoft Sentinel, and on the next page select our log analytics workspace and click "Add". After a moment, it should add our workspace to Sentinel.

![19 added to MS sentinetl](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/ebc6d65f-7d02-424d-81d4-4bba2e41eb77)

<h2>Configuring our VM via RDP</h2>  <br/>

Now we'll log into our VM via RDP to make sure everything is running and configured correctly. If your VM is done deploying, search for "Virtual machine" in the search tab and select the option. 

On the VM page, we should see our public IP address.

![20 copy ip to connect to RDP](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/c180d837-a25b-4cc8-b3a8-c89c5075056d)

Copy it, and search for RDP in the Windows start menu on your PC.

Open RDP, and paste the IP address you copied. Now remember the credentials you used when you created the VM, and use them to log in.

![21 login to RDP](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/ba4a3d93-0064-4ee2-9cac-110d86ded144)

Now, we'll see if we can ping our machine. 

In the Windows start menu (on our own machine) search for cmd, and open it.

In the Command Prompt, we'll attempt to ping our VM.

Type "ping (IP address here)" and see if you get a connection. 

![21 ping machine](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/d019eded-32a0-4cd0-9a44-c3ed9bab04eb)

It should time out. 

![22 timeout because firewall](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/a9dde464-3922-45a3-8bfc-b3b5c6a72afc)

The reason it's timing out is because of our VM firewall settings. We are going to turn it off.

In our VM, under the start menu search for wf.msc (windows firewall).

Once the firewall menu is open, select Windows Defender Firewall Properties.

![23 firewall config](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/b453130c-0115-425a-9a05-7d6b37be37c4)

Under the "Domain", "Private", and "Public" tabs turn off the firewall. 

![24 go to all tabs and turn firewall OFF](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/64230fdc-a6ea-4756-a5df-e6e3e098aa88)


<h2>Download powershell script</h2>
We need to be running a powershell script on our VM to actually log the geodata of the threat actors attempting to log into our VM.

What this script does essentially is it looks through our event logs, and notes the failed login attempts and their IPs. In conjunction with and API key from IPgeolocation.io, it maps the IP to a phsyical location.

Now, still in our VM, open your internet browser and navigate to: 

https://github.com/joshmadakor1/Sentinel-Lab/blob/main/Custom_Security_Log_Exporter.ps1

Select all and copy the script, and then open Powershell ISE by searching in the Start Menu for "Powershell" and selecting Powershell ISE.

In Powershell ISE, select new script

![26 new script](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/a8bc2289-a43e-4578-a562-4cc08927c217)

And paste in the script you copied. 

You'll notice at the beginning of the script there is a section for an API key. In order for this script to work, you'll need an API key from Ipgeolocation.io

You can sign up for free and get 1000 API requests daily. So do that, and in your dashboard you'll be provided an API key.

![29 API key](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/ee27bdfb-1cf3-482f-be36-47a614272eff)

Copy and paste the API key into your script.

Now, save your Powershell script to the desktop and name it whatever you'd like. In my case I named it "logexporter".

![31 save file](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/56d7a723-9653-462f-b7f9-2586900d61a8)

<h2>Create a custom log in Log Analytics Workspace</h2>  <br/>

The script is good to go, but we need to "train" analytics workspace to identify the data and process it. We will create a custom log inside of our Log Analytics Workspace that will allow us to bring the geodata into our workspace.

Back in Azure, search for "Log Analytics Workspace" and navigate to it. Select our workspace on the page. On the lefthand side select "Custom Logs", and then "Add custom log".

![34 select log file](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/fcb2ba58-199c-4eb2-a0f7-191517d743a0)

Here it's asking for a sample log, but it's on our VM.

In our VM, at the destination C:/ProgramData, we should have a file called "failed_rdp.log"

Open this file in notepad, and copy the contents of it. 

![30 example log training](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/7bcf1d12-ed53-4021-81a6-07ae434caad4)

*NOTE* Mine is super long because I've been running the script a while. You will only have a few sample entries. This is fine. Copy them.

Now on your Windows machine, create a new .log file called failed_rdp and paste the contents you copied from your VM into it. Save it anywhere. This is our sample we're using the train the log analytics what to look for in the log file.

Now point the sample log to where you saved "failed_rdp.log" on your Windows machine and select "Next".

![34 rdp log](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/b306666e-3e4c-4e12-921d-af1e2386bcdd)

Here we'll see an example of what it looks like. If it look good, select "Next".

![35 next](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/71b5f8f9-2ab5-4252-b6e1-a25f08f312b7)

Now in the collection path tab, we need to select where log resides in the VM. If you recall it should be C:/ProgramData/failed_rdp.log. 

![36 log location VM](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/81ed1058-35d5-410f-a445-696efd2b41ac)

Under "Details" you can name the custom log whatever you'd like. Select "Next", then "Create" at the bottom. It might take a moment for the Log Analytics Workspace and Sentinel to "sync". Just wait.

<h2>Create a new workbook in Sentinel</h2>
We are now at the final step. Congratulations for making it this far! 

What we need to do now, is create a workbook in Sentinel to show us the geodata of the failed logins on a map.

To do this, navigate to Sentinel and on the lefthand side select "Workbooks".

On this page select "+ Add Workbook".

![38 add workbook](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/6f97d954-bb3e-4141-a297-78b523b1625b)

On the "New Workbook" page, select "Edit". You can delete the existing widgets or keep them. I've kept them.

Still on this page, at the bottom select "Add" and from the dropdown menu select "Add Query".

![39add query](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/c00c9a68-f43a-4e4d-bf1c-bde7858cc602)

Copy and paste this query into the space. 

   failed_rdplog_CL
 |extend username = extract(@"username:([^,]+)", 1, RawData),
         timestamp = extract(@"timestamp:([^,]+)", 1, RawData),
         latitude = extract(@"latitude:([^,]+)", 1, RawData),
         longitude = extract(@"longitude:([^,]+)", 1, RawData),
         sourcehost = extract(@"sourcehost:([^,]+)", 1, RawData),
         state = extract(@"state:([^,]+)", 1, RawData),
         label = extract(@"label:([^,]+)", 1, RawData),
         destination = extract(@"destinationhost:([^,]+)", 1, RawData),
         country = extract(@"country:([^,]+)", 1, RawData)
 |where destination != "samplehost"
 |where sourcehost != ""
 |summarize event_count=count() by timestamp, label, country, state, sourcehost, username, destination, longitude, latitude

Also, under the visualization dropdown make sure to select "Map". You can set the map size to anything you'd like. I prefer "Full".

![40 map](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/2ca95b2b-244a-4929-bda1-c95246639b81)

Now you can run the query to make sure it works, (the map should show up) and save it. Name it anything you'd like.

Now, anytime you navigate to your workbook space you can see the map and failed logins and where they originated from.

*NOTE* For logs to be collected the script MUST be running, if it's not running there will be no new data displayed on your map.

After a few days of running my 1000 API requests, my map looked like this:

![45 WORLD MAPPU](https://github.com/TChungSEC/Azure_Sentinel_VM_SIEM_Map/assets/164605938/9a17dcc4-e99c-42cf-a268-c7b36d06328d)


Congratulations, you have completed this lab! You have set up a VM in Azure, opened it up to all traffic, and configured custom logs and analytics to view their geodata in Sentinel. 


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
