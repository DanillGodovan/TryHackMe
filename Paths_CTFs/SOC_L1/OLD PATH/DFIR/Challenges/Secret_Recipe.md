#### Prerequisites

[[Windows Forensics 1]]
[[Windows Forensics 2]]

## Storyline

Jasmine owns a famous New York coffee shop **Coffely** which is famous city-wide for its unique taste. Only Jasmine keeps the original copy of the recipe, and she only keeps it on her work laptop. Last week, James from the IT department was consulted to fix Jasmine's laptop. But it is suspected he may have copied the secret recipes from Jasmine's machine and is keeping them on his machine.

On the Desktop, there is a folder named Artifacts, which contains the registry Hives to examine and EZtools are included in system

#### Questions:

1. How many files are available in the Artifacts folder on the Desktop?
   **Answer:** 6

## Windows Registry Forensics

#### Questions:

1. How many files are available in the Artifacts folder on the Desktop?
   For this tasks we will use Registry Explorer by Eric Zimmerman
   Computer name is accessible at this path - `SYSTEM\ControlSet001\Control\ComputerName\ComputerName`
   **Answer:** JAMES
2. When was the **Administrator** account created on this machine? (Format: yyyy-mm-dd hh:mm:ss)
   For that purpose we will use SAM registry hive to look for all users present on machine
   The path is `SAM\Domains\Account\Users\Names\Administrator`. Look at the last timestamp.
   **Answer:** 2021-03-17 14:58:48
3. What is the RID associated with the Administrator account?
   At the same path we need to look for Value type. We will see this
   500 decimal is the RID that the account use
   **Answer:** 500
4. How many user accounts were observed on this machine?
   Just count all accounts that are given in Names Folder
   **Answer:** 7
5. There seems to be a suspicious account created as a backdoor with RID 1013. What is the account name?
   Here you need to scroll across all accounts present that can have RID 1013.
   **Answer:** bdoor
6. What is the VPN connection this host connected to?
   We need to look for NetworkList in `Software` Hive
   `Microsoft\Windows NT\CurrentVersion\NetworkList`
   **Answer:** ProtonVPN
7. When was the first VPN connection observed? (Format: YYYY-MM-DD HH:MM:SS)
   Basically we need to look now at First Connect Local
   **Answer:** 2022-10-12 19:52:36
8. There were three shared folders observed on his machine. What is the path of the third share?
   We can find them in `SYSTEM` hive at this path: `[\SYSTEM\ControlSet001\Services\LanmanServer\Shares]`
   **Answer:** C:\RESTRICTED FILES
9. What is the last DHCP IP assigned to this host?
   We need to look in `SYSTEM` Hive but in other place
   `SYSTEM\ControlSet0001\Services\Tcpip\Parameters\Interfaces`
   **Answer:** 172.31.2.197
10. The suspect seems to have accessed a file containing the secret coffee recipe. What is the name of the file?
    We need to check the evidence of execution. Therefore we will use `NTUSER.DAT`
    `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`
    **Answer:** secret-recipe.pdf
11. The suspect executed multiple commands using the Run window. What command was used to enumerate the network interfaces?
    Let's check `Software` Registry Hive
    `NTUSER.DAT/Software/Microsoft/Windows/CurrentVersion/Explorer/RUNMRU`
    **Answer:** pnputil /enum-interfaces
12. The user searched for a network utility tool to transfer files using the file explorer. What is the name of that tool?
    `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery`
    **Answer:** netcat
13. What is the recent text file opened by the suspect?
    Go back to RecentDocs and search for .txt files
    **Answer:** secret-code.txt
14. How many times was PowerShell executed on this host?
    We need to look for this path: `NTUSER.DAT\Software\Microsoft\Windows \Currentversion\Explorer\UserAssist\{GUID}\Count` and check for the first count given, then search exact for powershell.exe
    **Answer:** 3
15. The suspect also executed a network monitoring tool. What is the name of the tool?
    Analyze each tools given in this folder, only suitable to question is Wireshark
    **Answer:** WireShark
16. Registry Hives also note the amount of time a process is in focus. Examine the Hives and confirm for how many seconds was ProtonVPN executed?
    Return back to `UserAssist` in NTUSER.dat and look for the focus time on ProtonVPN
    **Answer:** 343
17. Everything.exe is a utility used to search for files in a Windows machine. What is the full path from which everything.exe was executed?
    Search for Everything.exe in Registry Explorer in UserAssist.
    **Answer:** C:\Users\Administrator\Downloads\tools\Everything\Everything.exe
