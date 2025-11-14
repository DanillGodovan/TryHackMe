## Incident Scenario

Our user "Hattori" has reported strange behavior on his computer and realized that some PDF files have been encrypted, including a critical document to the company named `important_document.pdf`. He decided to report it; since it was suspected that some credentials might have been stolen, the `DFIR` team has been involved and has captured some evidence. Join the team to investigate and learn how to get information from a memory dump in a practical scenario.

#### Learning Objectives

In this room, we'll cover the following learning objectives.

- Memory Forensics’ basic concepts.
- How to access and set up the environment.
- Gathering information from the compromised target.
- Search for suspicious activity using the information obtained.
- Extracting and analysing data from memory.
- Conclusion & further steps after completing the room.

#### Prerequisites

Learning Prerequisites
An understanding of the following topics is recommended before starting the room:

- [[Volatility]]
- [[IR Difficulties and Challenges]]

## Memory Forensics

In Cyber Security, Memory Forensics is a subset of computer forensics that analyzes volatile memory, usually on a compromised machine. In Windows OS it corresponds to the Random Access Memory (RAM) and its content is flushed with every reboot or shutdown, making it one of the usual initial task to perform during an incident. This provides us the information what processes were run at the exact time.

During the memory acquisition phase we'll copy the live memory to a file, commonly referred to as a dump to perform the analysis without risking losing the data from an inadvertent reboot on the compromised system.

#### Questions

1. What type of memory is analyzed during a forensic memory task?
   **Answer:** RAM
2. In which phase will you create a memory dump of the target system?
   **Answer:** Memory Acquisition

## Environment & Setup

There are several tools to acquire the memory from the target machine if needed; several tools can help us:

- Windows - FTK Imager, WinPmem
- Linux - LIME
- macOS - osxpmem

A memory dump named `memdump.mem` will be present at the home address at `/home/analyst` For the analysis we will use [[Volatility]]3 and following plugins:

- Windows.cmdline - Lists process command line arguments
- windows.drivermodule - Determines if any loaded drivers were hidden by a rootkit
- Windows.filescan - Scans for file objects present in a particular Windows memory image
- Windows.getsids - Print the SIDs owning each process
- Windows.handles - Lists process open handles
- Windows.info - Show OS & kernel details of the memory sample being analyzed
- Windows.netscan - Scans for network objects present in a particular Windows memory image
- Widnows.netstat - Traverses network tracking structures present in a particular Windows memory image.
- Windows.mftscan - Scans for Alternate Data Stream
- Windows.pslist - Lists the processes present in a particular Windows memory image
- Windows.pstree - List processes in a tree based on their parent process ID

#### Questions:

1. Which plugin can help us to get information about the OS running on the target machine?
   **Answer:** Windows.info
2. Which tool referenced above can help us take a memory dump on a Linux OS?
   **Answer:** LIME
3. Which command will display the help menu using Volatility on the target machine?
   **Answer:** vol -h

## Gathering Target Information

#### Obtaining Information

Getting information about the target is crucial to our investigation since it ensures we're analyzing the correct context and environment of the evidence. We can understand specific architecture and operating systems and ensure that our findings are accurate, relevant and legitim.

Use -f switch to get information about the file to analyze with the plugin windows.info

```
vol -f memdump.mem windows.info
```

The output above shows relevant information to identify the machine we are working on, such as architecture, number of processors, and version

#### Questions

1. Is the architecture of the machine x64 (64bit) Y/N?
   **Answer:** Y
   Is64Bit Parameter is true
2. What is the Verison of the Windows OS
   **Answer:** 10
   NtMajorVersion Parameter gives us an answer
3. What is the base address of the kernel?
   **Answer:** 0xf8066161b000
   Check Kernel Base Parameter to get the answer

## Searching for Suspicious Activity

Suspicious activity is often unexpected processes or unusual network connections or registry modifications.

#### Starting the Search

We can use `windows.netstat` to look for unusual network connections such as websites or remote access connections

```
vol -f memdump.mem windows.netstat
```

From the output above, we can observe a connection established on port `3389` from the IP `192.168.182.139` with timestamp `2024-02-24 22:47:52.00` ; this could indicate an attacker's initial access.

Next we can look for the processes with `windows.pstree`

```
vol -f memdump.mem windows.pstree
```

But how can we identify a suspicious process? One of the most common ways is to check the name of the process; threat actors commonly use names to try to disguise the execution
Considering the above and looking again at the output, we can observe a process with the truncated name `critical_updat`, as shown below.

#### Questions

1. Using the plugin "windows.netscan". Can you identify the destination IP address where a connection is established on port 80?
   **Answer:** 192.168.182.128
   Look for established connections in the output of the command and search for port 80
2. Using the plugin "windows.netscan," can you identify the program (owner) used to access through port 80?
   **Answer:** msedge.exe
   The answer is the program that uses this connection
3. Analyzing the processes present on the dump, what is the PID of the child process of critical_updat?
   **Answer:** 1612
   PID is left from the Parent Process ID, this process is below the critical_updat
4. What is the time stamp time for the process with the truncated name critical_updat?
   **Answer:** 2024-02-24 22:51:50.000000
   Just look at the time process was created at his graph.

## Finding Interesting Data

With the information we have collected, we can investigate the process `critical_updat`  that we identified in our previous task, which has a child process called `updater`**.** Let's investigate the child process more in-depth. Let's start by looking at where on the disk it was saved; for that, we can use the plugin `windows.filescan` which will allow us to examine the files accessed that are stored in the memory dump. This output is quite big, so to access the data in a better way, we will use the `>` character in bash to redirect the output to a file, in this case, `filescan_out`.

```
vol -f memdump.mem windows.filescan > filescan_out
```

Afterwards we can inspect any data using `cat` with `grep` to sort it
Above, we can observe that the files have been stored in the Directory `\Users\user01\Documents\updater.exe` or `C:\Users\user01\Documents\updater.exe`
If we want to have more detailed information like when the file was accessed or modified, we can use the plugin `windows.mftscan.MFTScan`. Then you can search for updater.exe with grep

```
cat mftscan_out | grep updater
```

From the output above, we observe the last four timestamps correspond to the Created, Modified, Updated, and Accessed TimeStamps; we can take notes of those.

#### Getting the Goods

Let's get the information on the process. Now we will dump the memory region corresponding to updater.exe and examine it. Therefore we'll use `windows.memmap` plugin with specifying output switch -o. In this case we will use the same directory denoted by the character `.` and the option --dump followed by option --pid

```
vol -f memdump.mem -o . windows.memmap --dump --pid 1612
```

Examining the file is difficult since it contains non-printable characters, so we'll use the strings command to analyze the output better. Since we have the file strings available to us now, we could look for key patterns like `HTTP` or `key` or any pattern that can lead us quickly to an artefact. Another way to scroll the terminal is by using the `strings` command piped to `less` to navigate through the output as shown in the command output below

```
strings pid.1612.dmp |less
```

We see already a possible key and a domain from a URL that the process may have accessed. By scrolling down we see more indications that this is a malicious process since we can find the `important_document.pdf` filename
We can now sort the output to find requests to above shown domain

```
strings pid.1612.dmp |grep -B 10 -A 10 "http://key.critical-update.com/encKEY.txt"
```

#### Questions

1. Analyzing the "windows.filescan" output, what is the full path and name for critical_updat?
   **Answer:** C:\Users\user01\Documents\critical_update.exe
   Use the output of filescan and search for critical_updat with `grep`
2. Analyzing the "windows.mftscan.MFTScan" what is the Timestamp for the created date of important_document.pdf?
   **Answer:** 2024-02-24 20:39:42.000000
   Search it in mftscan_out using `grep` and `cat`
3. Analyzing the updater.exe memory output, can you observe the HTTP request and determine the server used by the attacker?
   **Answer:** SimpleHTTP/0.6 Python/3.10.4
   Examine the output of process dump using strings and given suspicious link

## Conclusion and Wrapping Up

In this scenario, we have put into practice the skills necessary to start digging into the world of memory forensics and practice with a tool like volatility, which is widely used among digital forensics professionals.
While all the information presented in his room can be used in real-life scenarios, it is possible to delve more deeply into the topic by examining more complex attacks. Some interesting material to get more into it can be the following:

- [Windows Forensics 2](https://tryhackme.com/r/room/windowsforensics2)
- [Linux Forensics](https://tryhackme.com/r/room/linuxforensics)
- [iOS Forensics](https://tryhackme.com/r/room/iosforensics)
- [Windows Applications Forensics](https://tryhackme.com/r/room/windowsapplications)
