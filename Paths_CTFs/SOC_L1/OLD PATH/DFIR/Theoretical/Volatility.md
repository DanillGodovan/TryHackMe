Volatility is a free memory forensics tool developed and maintained by Volatility Foundation, commonly used by malware and SOC analysts within a blue team or as part of their detection and monitoring solutions. Volatility is written in Python and is made up of python plugins and modules designed as a plug-and-play way of analyzing memory dumps.

## Volatility Overview

Volatility is the world's most widely used framework for extracting digital artifacts from volatile memory (RAM) samples. It offers visibility into the runtime state of the system. Volatility is built off of multiple plugins working together to obtain information from the memory dump

Volatility is built off of multiple plugins working together to obtain information from the memory dump. To begin analyzing a dump, you will first need to identify the image type

## Installing Volatility

Command used: `git clone https://github.com/volatilityfoundation/volatility3.git`
You now have Volatility installed!
To test your installation run the `vol.py` file with the help parameter.
Command used: `python3 vol.py -h`

## Memory Extraction

We can extract memory from a bare-metal machine by using these instruments:

- FTK Imager
- [[Redline]]
- DumpIt.exe
- win32dd.exe / win64dd.exe
- Memoryze
- Fastdump
  By extracting on a bare-metal host it can usually take a considerable amount of time. Take this into consideration during your investigation if time is a constraint
  Most of this instruments will return a .raw file or their own format

For virtual machines (VM), gathering a memory file can easily be done by collecting the virtual memory file from the host machine's drive. This file can change because of the hypervisor used.

- VMWare - .vmem
- Hyper-V - .bin
- Parallels - .mem
- VirtualBox - .sav file

## Plugins Overview

Volatility3 simplified the process of choice of the plugin because host name and specific OS profile is now checked by the software. You just need to specify the operating system like `windows.info` or `linux-info`. There are several plugins available with Volatility as well as third-party plugins.

### Identifying Image Info and Profiles

By default, Volatility comes with all existing Windows Profiles from Windows XP to Windows 10
Image profiles can be hard to determine if you don't know exactly what version and build the machine you extracted from was. Therefore here's imageinfo plugin that helps us to identify the OS information about memory dump, also `linux-info` or `windows.info`

## Listing Processes and Connections

There is five different plugins within Volatility that allow you to dump processes and network connections.

The most basic way of listing processes is enumerating them using `pslist`; this plugin will get the list of processes from the doubly-linked list that keeps track of processes in memory
Syntax: `python3 vol.py -f <file> windows.pslist`

Despite that, some malware, typically rootkits, will, in an attempt to hide their processes, unlink itself from the list. By unlinking themselves from the list you will not see their processes when using `pslist`. We can use `psscan` technique of listing processes. It will locate processes by finding data structures that match \_EPROCESS. It can also cause false positives
Syntax: `python3 vol.py -f <file> windows.pstree`

The third plugin can show us tree of processes based on their parent process ID using the same methods as `pslist`.
Syntax: `python3 vol.py -f <file> windows.pstree`

To identify the network connections present at the time of extraction on the host machine we can use `netstat` plugin to identify all memory structures with a network connection
Syntax: `python3 vol.py -f <file> windows.netstat`
This command despite of that is very unstable. It is preferable to use tools like bulk_extractor to extract a PCAP file from a memory file.

The last plugin we cover is `dlllist`. This plugin will list all DLLs associated with processes at the time of extraction.
Syntax: `python3 vol.py -f <file> windows.dlllist`

## Volatility Hunting and Detection Capabilities

Volatility offers a plethora of plugins that can be used to aid in your hunting and detection capabilities when hunting for malware or other anomalies.

It is recommended to have a basic understanding of how evasion techniques and various malware techniques are employed by adversaries, as well as how to hunt and detect them before going through this section.

The plugin `malfind` is most useful when hunting for code injection. This plugin will attempt to identify injected processes and their PIDs along with the offset address and a Hex, Ascii and Disassembly view of the infected area.
Syntax: `python3 vol.py -f <file> windows.malfind`

To utilize YARA rules we can use `yarascan` plugin to search for strings and patterns.
Syntax: `python3 vol.py -f <file> windows.yarascan`

## Advanced Memory Forensics

Advanced Memory Forensics designed to find malware that is using various evasion techniques.

The first evasion technique we will be hunting is hooking. There are five methods of hooking employed by adversaries, outlined below:

- SSDT Hooks
- IRP Hooks
- IAT Hooks
- EAT Hooks
- Infline Hooks
  The most common technique is the SSDT Hooks when dealing with malware evasion.
  The `ssdt` plugin will search for hooking and output is results. Hooking can be used by legitimate applications, so it up to you as the analyst to identify what is evil. SSDT stands for System Service Descriptor Table. An adversary can hook into this table and modify pointers to point to a location the rootkit controls.

It is preferable to use ssdt after Basic Memory Forensics.
Syntax: `python3 vol.py -f <file> windows.ssdt`

Adversaries can also use drivers as the part of their evasion. There are 2 plugins to seek for that kind of evidence - `modules` and `driverscan`

The `modules` plugin will dump a list of loaded kernel modules. This can help identifying active malware. `driverscan` will scan for drivers present on the system at the time of extraction. This plugin can help to identify driver files in the kernel that the `modules` plugin might have missed or were hidden.
Syntax: `python3 vol.py -f <file> windows.driverscan`
Syntax: `python3 vol.py -f <file> windows.modules`

There are also other plugins that might be helpful to hunt for advanced malware in memory:

- modscan - Scan for hidden or residual modules
- driverip - driver dispatch (IRP) tables
- callbacks - display kernel callbacks, notification routines
- idt - audit the IDT
- apihooks - API hooks
- moddump - dump a kernel module
- handles - Display open handles

## Practical Investigations

#### Case 001 - BOB! THIS ISN'T A HORSE!

Your SOC has informed you that they have gathered a memory dump from a quarantined endpoint thought to have been compromised by a banking trojan masquerading as an Adobe document. Your job is to use your knowledge of threat intelligence and reverse engineering to perform memory forensics on the infected host.

You have been informed of a suspicious IP in connection to the file that could be helpful. `41.168.5.140`

The memory file is located in `/Scenarios/Investigations/Investigation-1.vmem`

#### Case 002 - That Kind of Hurt my Feelings

You have been informed that your corporation has been hit with a chain of ransomware that has been hitting corporations internationally. Your team has already retrieved the decryption key and recovered from the attack. Still, your job is to perform post-incident analysis and identify what actors were at play and what occurred on your systems. You have been provided with a raw memory dump from your team to begin your analysis.

The memory file is located in `/Scenarios/Investigations/Investigation-2.raw`

#### Questions:

1. What is the build version of the host machine in Case 001?
   **Answer:** 2600.xpsp.080413-2111
   Scan the case 001 for the information by using windows.info
2. At what time was the memory file acquired in Case 001?
   **Answer:** 2012-07-22 02:45:08
   Analyze the dump with windows.info plugin
3. What process can be considered suspicious in Case 001?  
   Note: Certain special characters may not be visible on the provided VM. When doing a copy-and-paste, it will still copy all characters.
   **Answer:** reader_sl.exe
   Look all presented processes in pslist plugin. reader_sl.exe is the only suspicious here.
4. What is the parent process of the suspicious process in Case 001?
   **Answer:** use the pstree plugin to see what parent process ID connected to reader_sl.exe
   Here we have explorer.exe
5. What is the PID of the suspicious process in Case 001?
   **Answer:** Just copy the Process ID of reader_sl.exe - 1640
6. What is the parent process PID in Case 001?
   **Answer:** check what process connected to reader_sl.exe - 1484
7. What user-agent was employed by the adversary in Case 001?
   **Answer:** Mozilla/5.0 (Windows; U; MSIE 7.0; Windows NT 6.0; en-US)
   Use netstat plugin to investigate network processes
8. Was Chase Bank one of the suspicious bank domains found in Case 001? (Y/N)
   Answer: Y
   By checking all requests we can notice his domain which increases our suspense about Chase Bank
9. What suspicious process is running at PID 740 in Case 002?
   **Answer:** @WanaDecryptor@
   Check directly by using `pstree` plugin in this case
10. What is the full path of the suspicious binary in PID 740 in Case 002?
    **Answer:** C:\Intel\ivecuqmanpnirkt615\@WanaDecryptor@.exe
    Sort this process by using `grep` and use `dlllist` to see what binary path was.
11. What is the parent process of PID 740 in Case 002?
    **Answer:** tasksche.exe
    Go again to `pstree` and we see Parent Process by @WanaDecryptor@
12. What is the suspicious parent process PID connected to the decryptor in Case 002?
    **Answer:** 1940
    Check the process ID of tasksche.exe
13. From our current information, what malware is present on the system in Case 002?
    **Answer:** Wannacry
    Google the name and you become the answer
14. What DLL is loaded by the decryptor used for socket creation in Case 002?
    **Answer:** Ws2_32.dll
    Check `dlllist` and sort all of @WanaDecryptor using `grep`. Then with help of Windows Documentation you need to analyze what binary is for Sockets responsible.
15. What mutex can be found that is a known indicator of the malware in question in Case 002?
    **Answer:** MsWinZonesCacheCounterMutexA
    use `handles` plugin and check all handles used in @WanaDecryptor@ using search engine
16. What plugin could be used to identify all files loaded from the malware working directory in Case 002?
    **Answer:** windows.filescan
    We need to search for this plugin in the documentation of Volatility.

## Conclusion

We have only covered a very thin layer of memory forensics that can go much deeper when analyzing the Windows, Mac, and Linux architecture. If you're looking for a deep dive into memory forensics, I would suggest reading: The Art of Memory Forensics.

There are also a number of wikis and various community resources that can be used for more information about Volatility techniques found below.

- [](https://github.com/volatilityfoundation/volatility/wiki)[https://github.com/volatilityfoundation/volatility/wiki](https://github.com/volatilityfoundation/volatility/wiki)
- [](https://github.com/volatilityfoundation/volatility/wiki/Volatility-Documentation-Projec)[https://github.com/volatilityfoundation/volatility/wiki/Volatility-Documentation-Projec](https://github.com/volatilityfoundation/volatility/wiki/Volatility-Documentation-Projec)

## CHEATSHEET

    https://downloads.volatilityfoundation.org/releases/2.4/CheatSheet_v2.4.pdf
