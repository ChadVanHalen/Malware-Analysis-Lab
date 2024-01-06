<h1>Malware Analysis Lab<br/>
Dynamic Analysis</h1>

<h2>Description</h2>
This project consists of getting hands-on experience dealing with malware in a quarantined VM network and using tools like WireShark, ProcMon, and Cutter to perform both static and dynamic analysis<br/>
After getting info on the malware prior to detonation, the next step is to safely detonate the malware and use tools to track what it does, then write a YARA rule based off our findings.<br />


<h2>Utilities Used</h2>
 
- <b>ProcMon</b>
- <b>INetSim</b>
- <b>WireShark</b>
- <b>PowerShell</b>
- <b>NotePad++</b>

<h2>Environments Used </h2>

- <b>Windows 10 - FlareVM</b>
- <b>Ubuntu Server 22.04.1 - REMnux</b>

<h2>Project walk-through:</h2>

<p align="center">
Now that I'm in detonation territory, need to triple check that the VM has zero internet and is not connected to my home LAN:<br/>
<img src="https://i.postimg.cc/jjJ4yKpq/1-Before-we-detonate-making-sure-that-we-are-disconnected-from-the-network.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/hv4mZq5d/2-Proc-Mon-and-INet-Sim-from-remnuxpng.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
After opening ProcMon and executing the binary, the file removed itself and I see ProcMon display some of the functions being carried out by the malware<br/>
In the "Process Tree" menu I can see the malware executable still running and there is a console host running from it:<b/>
<img src="https://i.postimg.cc/vBsf9n7X/3-After-detonating-the-exe-deleted-itself.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/52XBMbN5/4-Proc-Mon-Process-Tree-still-see-file-running-and-cmd-running-under-it.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Clicking on the Conhost process ProcMon will give me the command it's running: <br/>
<img src="https://i.postimg.cc/V6QBRpwq/5-Proc-Mon-will-even-take-note-of-what-command-was-being-run-will-have-to-investiagte.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
I can sort and filter to better parse through the data, here I sorted by processes ran by the malware file, and can see what it did and in what order: <br/>
<img src="https://i.postimg.cc/zfWTV7hV/6-Sorting-Proc-Mon-to-only-include-invoice-we-can-better-see-what-it-did-in-what-order-like-here-it-l.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
I can further filter to find specific processes, like sorting for "RegSetValue" to find instances of it messing with the registry. Here it looks like it is tying itself to the "Google Update": <br/>
<img src="https://i.postimg.cc/9MdPNbXv/7-Filtering-for-Reg-Set-Values-to-find-a-Google-Update-value.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Next I'm going to use WireGuard to see if I catch the malware calling out on the network to a C2 server.<br/>
And I do see an HTTP request to this website: <br/>
<img src="https://i.postimg.cc/zvpCqfg7/8.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/ZnJPkNC0/9-To-do-this-we-spin-up-Wire-Shark.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/rmNW9pQT/10-Detonating-we-see-some-HTTP-requests-to-this-random-website.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Not seeing anything too crazy on VirusTotal, and the Wayback Machine has no hits.<br/>
Using nslookup to search for the IP directly doesn't lead me to any particularly interesting results either. Not sure what this HTTP request is for, but I will still keep note of it: <br/>
<img src="https://i.postimg.cc/fWvjc8rd/11-Nothing-crazy-out-of-the-ordinary-and-no-hits-on-wayback-machine.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/MKK0d1CF/12-nslookup-resolves-these-addresses.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
So now that I have some detection indicators from the static and dynamic analysis process I will create a very basic YARA detection rule I can add to my EDR to help catch this in the future<br/>
This rule is defining 3 strings:<br/>
-The file name being "invoice_231..."<br/>
-The suspicious "CreateFileA" function I investigated in the last section<br/>
-The "magic byte", which in executables is "MZ". Defining this will help look specifically for executables.<br/><br/>

If these 3 strings are met, it will trigger the YARA rule in my security program. From there I can define what to do if this rule is triggered, like just creating an alert, to a fully scripted process to remove the file.<br/>
<img src="https://i.postimg.cc/MKP74vxK/13-Create-yara-rule.png" height="80%" width="80%" alt="SOC Analyst Lab"/>




<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
