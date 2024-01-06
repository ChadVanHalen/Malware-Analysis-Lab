<h1>Malware Analysis Lab<br/>
 Static Analysis</h1>

<h2>Description</h2>
This project consists of getting hands-on experience dealing with malware in a quarantined VM network and using tools like WireShark, ProcMon, and Cutter to perform both static and dynamic analysis<br/>
In this section I will be using multiple different tools to statically poke at the malware BEFORE it is detonated. Doing this will provide me "fingerprints" I can compare with on online resources like VirusTotal,<br/>
as well as get ideas of what kind of code is being executed and what might happen if the malware is detonated on a system
<br />


<h2>Utilities Used</h2>
 
- <b>PowerShell</b>
- <b>VirusTotal</b>
- <b>PEStudio</b>
- <b>Floss</b>
- <b>Capa</b>

<h2>Environments Used </h2>

- <b>Windows 10 - FlareVM</b>
- <b>Ubuntu Server 22.04.1 - REMnux</b>

<h2>Project walk-through:</h2>

<p align="center">
First step now that I have the malware on the system is I'm going to check it against VirusTotal, as well as check it's various hashes:<br/>
<img src="https://i.postimg.cc/mD55KZHQ/13-We-can-examine-the-file-through-Virus-Total.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/T2sz40VZ/14-VT-hash-output.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Next I'll use one of the Flare VM tools called PEStudio. Feeding the malware into it will give me more info to build on, including the hash that I can compare with to the one from VirusTotal:<b/>
<img src="https://i.postimg.cc/Gt4ZpTC7/15-Running-the-binary-through-pestudio-to-get-further-hashing-fingerprints.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
One of the first things PEStudio allows me to check is the "raw" and "virtual" size of the binary<br/>
Malware authors will often compress their executables to hide certain functinoalities that will be flagged by anti-malware software, so I can use these two sizes as an indication of if it is being compressed or not<br/>
If they are relatively the same, the binary is likely not being compressed. If they vary wildly, it is likely being compressed. In this example, the binary is likely not being compressed: <br/>
<img src="https://i.postimg.cc/pLf3ytwH/16ONEO-1.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
In the "strings" section, PEStudio will parse through all of the string data in the binary and flag potentially malicious sequences. In this example it's flagging values like "WriteFile"<br/>
I will keep this info handy in a notebook for further review: <br/>
<img src="https://i.postimg.cc/2yLKYpQg/17-In-strings-section-pestudio-can-pull-and-flag-strings-that-may-correlate-to-malicious-actions-lik.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
But I can see other strings that weren't flagged that might be used maliciously, like dlls relating to KERNEL32 and USER32. I will also keep these in the notebook for further review.<br/>
One other thing I want to note here are the strings of random characters that accompany each one of these dlls. It may be these are randomly generated to cover up actual code, will need to look into it later:  <br/>
<img src="https://i.postimg.cc/xCCZsNcf/18.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
In the "libraries" section I can see these dlls were collected as potential libraries of interest, confirming my want to be suspicious correct:  <br/>
<img src="https://i.postimg.cc/FKGqgVFC/19-Libraries-tab-also-highlights-the-32-dills.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Moving to another tool installed with Flare, Floss, I can see similar info, just output in a slightly different way<br/>
I wouldn't use each of these tools together, this is more demonstrating different tools provide the same info, but some run on the command line, some of GUIs, etc:  <br/>
<img src="https://i.postimg.cc/YjcyqX80/20-Running-Floss-if-the-malware-was-packed-Floss-has-functionality-to-help-with-that-but-we-dont-nee.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/MZhWQqFM/21-Floss-output-the-same-strings-seen-in-pestudio-into-a-txt-file-allowing-manipulation-and-search.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Within the Floss output to the txt file from the last step we can search the raw plaintext for indications of URLs. Searching for .org, .net, .com only brought one result "corect.com"<br/>
Due to the incorrect spelling, I will also put this in my notebook to investigate further as well:  <br/>
<img src="https://i.postimg.cc/xCBSM3tD/22-Looking-for-URLs-only-finding-the-previously-seen-corect-not-other-com-net-org-etc.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
The next tool I'll be utilizing is Capa<br/>
Capa is useful because it will output data in reference to MITRE ATT&CK frameworks<br/>
In this example, it is telling me different tactics and techniques this malware could be associated with.<br/>
In this case I can look up the ID number T1497.001 to find documentation about this type of malware from MITRE: <br/>
<img src="https://i.postimg.cc/BZ8SbXMf/23-Capa-is-another-resource-to-get-info-like-the-hash-but-also-will-try-to-tie-to-malware-into-MITRE.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/hPTK86kC/24-Cross-referencing-Capa-output-to-attack-mitre-org.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Now that I have a bunch of info sourced from these static readings, I will do some research on them.<br/>
First off I will investigate this "corect.com" I saw earlier. VT shows no signs of it be flagged currently, so I use the wayback machine to check it out from around when this malware was captured, 2013<br/>
The site appears to be a legitimate Romanian news site of some type. Not exactly something throwing up too many red flags, but might help point to the origination of the malware:  <br/>
<img src="https://i.postimg.cc/43NZgdmM/26-Now-that-we-have-a-bunch-of-info-we-can-start-doing-research-like-on-corect-com.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/y8KsLnB7/27-Since-we-know-this-specific-malware-was-from-2013-we-can-go-check-the-wayback-machine-to-see-of-c.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/50XVXR5S/28-The-site-appears-to-be-Romanian-according-to-Google-translate.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br/>
<br/>
Next on my "to-research" notebook I pulled earlier is checking some of the strings that were flagged and what they might do:  <br/>
<img src="https://i.postimg.cc/fRvZpXTm/29-Next-checking-on-some-of-the-strings-that-were-flagged-do-some-research-on-what-they-could-be-use.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/vmqyHsnm/30-Research-on-the-mul-div-dills.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br/>
<br/>
After these more basic looks at the static malware, the next steps are going to be slightly more advanced and in-depth static analysis<br/>
Here I will be using Cutter, which also provides the same info the other tools did, but will also allow much more granular look into the actual code:  <br/>
<img src="https://i.postimg.cc/26Y1jq5d/1-Using-Cutter-to-start-more-advanced-static-analysis-of-the-malware.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/bvcJLG1X/2.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br/>
<br/>
Here is the malware's assembly code, I can use this to pinpoint some of the exact suspicious things found in previous steps and tie it to the actual code<br/>
Along the bottom I can search for specific strings. This is a good point to research the seemingly random strings that accompanied the dlls I noted earlier:  <br/>
<img src="https://i.postimg.cc/VLh5rZ6k/3-Cutter-also-lets-us-look-into-the-assembly-code-and-we-can-better-pinpoint-our-suspected-API-calls.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/x1b8KGJy/4-Graphical-view-of-the-code.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/90FzTvZr/5.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br/>
<br/>
Search for that string brings me to this piece of code. It looks like it's specifically using the string 'ighC' within the full string of 'CellrotoCrudIntohighCols'.<br/>
So back in the search I go to search for the dll file that was next to that string, CreateFileA, just to test if it brings me to the same piece of code:  <br/>
<img src="https://i.postimg.cc/VvGvFDW4/6-Clicking-on-the-string-takes-us-here-and-we-can-see-some-of-the-string-namely-the-igh-C-portion.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/CL5zKpw2/7-Back-in-string-search-we-can-test-our-hypothesis-of-the-kernel-next-to-the-string-of-words-being-p.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br/>
<br/>
To no surprise it does. So it seems I can hypothesize that the strange, likely randomly generated, string characters are being used to smoke screen the actual string used for specific purposes<br/>
This practice seems to have been successful, since in the earlier step PEStudio did not flag these functions, these were manually spotted by me:  <br/>
<img src="https://i.postimg.cc/3rnk7xQp/8.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
