## Introduction to Windows Forensics

Computer forensics is a key branch of cybersecurity focused on collecting and analyzing digital evidence to uncover activities performed on computers. It’s part of the broader field of digital forensics, which examines data from all types of digital devices for use in criminal, civil, or corporate investigations.

A famous case highlighting its importance is the BTK serial killer investigation, where forensic experts traced a deleted Word document on a floppy disk to identify the criminal.

Since Windows dominates around 80% of the desktop market, understanding Windows forensics is crucial. Analysts can examine **forensic artifacts** — traces of user activity like registry entries or system logs — to reconstruct events and behaviors on a system.

While Windows records extensive user activity, this tracking mainly exists to personalize the user experience (e.g., saving settings, layouts, and app preferences). However, forensic investigators can use this same data to understand what actions were performed on a device.

The upcoming sections focus on how the **Windows Registry** and other system locations store these artifacts, and how they can be analyzed during an investigation.

## Windows Registry and Forensics

The Windows Registry is a collection of databases that contains the system's configuration data. This configuration data can be about the hardware, the software or the user's information. It also includes data about the recently used filed, programs used or devices connected to the system.

This registry consists of Keys and Values. When you open the **regedit.exe** (Registry Editor) utility to view the registry, the folders you see are **Registry Keys**. Registry Value are stored in these keys.

#### Structure of the Registry:

The registry on any Windows system contains the following five root keys:

1. HKEY_CURRENT_USER
2. HKEY_USERS
3. HKEY_LOCAL_MACHINE
4. HKEY_CLASSES_ROOT
5. HKEY_CURRENT_CONFIG

**HKEY_CURRENT_USER** Contains the root of the configuration information for the user who is currently logged on. The user's folders, screen colors, and Control Panel settings are stored here. This information is associated with the user's profile. This key is sometimes abbreviated as HKCU.

**HKEY_USERS** Contains all the actively loaded user profiles on the computer. HKEY_CURRENT_USER is a subkey of HKEY_USERS. HKEY_USERS is sometimes abbreviated as HKU.

**HKEY_LOCAL_MACHINE** Contains configuration information particular to the computer (for any user). This key is sometimes abbreviated as HKLM.

**HKEY_CLASSES_ROOT** is a subkey of `HKEY_LOCAL_MACHINE\Software` . The information that is stored here makes sure that the correct program opens when you open a file by using Windows Explorer. This key is sometimes abbreviated as HKCR.<br><br>Starting with Windows 2000, this information is stored under both the HKEY_LOCAL_MACHINE and HKEY_CURRENT_USER keys. The `HKEY_LOCAL_MACHINE\Software\Classes` key contains default settings that can apply to all users on the local computer.  The `HKEY_CURRENT_USER\Software\Classes` key has settings that override the default settings and apply only to the interactive user.<br><br>The HKEY_CLASSES_ROOT key provides a view of the registry that merges the information from these two sources. HKEY_CLASSES_ROOT also provides this merged view for programs that are designed for earlier versions of Windows. To change the settings for the interactive user, changes must be made under `HKEY_CURRENT_USER\Software\Classes` instead of under HKEY_CLASSES_ROOT.<br><br>To change the default settings, changes must be made under `HKEY_LOCAL_MACHINE\Software\Classes`  .If you write keys to a key under HKEY_CLASSES_ROOT, the system stores the information under `HKEY_LOCAL_MACHINE\Software\Classes` .<br><br>If you write values to a key under HKEY_CLASSES_ROOT, and the key already exists under `HKEY_CURRENT_USER\Software\Classes` , the system will store the information there instead of under `HKEY_LOCAL_MACHINE\Software\Classes` .

**HKEY_CURRENT_CONFIG** Contains information about the hardware profile that is used by the local computer at system startup.

#### Questions:

1. What is the short form for **HK**EY***L**OCAL***M**ACHINE?
   **Answer:** HKLM

## Accessing registry hives offline

If you are accessing a live system, you will be able to access the registry using regedit.exe
The majority of these hives are located in the `C:\Windows\System32\Config` directory and are:

1. **DEFAULT** (mounted on `HKEY_USERS\DEFAULT`)
2. **SAM** (mounted on `HKEY_LOCAL_MACHINE\SAM`)
3. **SECURITY** (mounted on `HKEY_LOCAL_MACHINE\Security`)
4. **SOFTWARE** (mounted on `HKEY_LOCAL_MACHINE\Software`)
5. **SYSTEM** (mounted on `HKEY_LOCAL_MACHINE\System`)

#### Hives containing user information:

Apart from these hives, two other hives containing user information can be found in the User profile directory. For Windows 7 and above, a user’s profile directory is located in `C:\Users\<username>\` where the hives are:

1. **NTUSER.DAT** (mounted on HKEY_CURRENT_USER when a user logs in)
2. **USRCLASS.DAT** (mounted on HKEY_CURRENT_USER\Software\CLASSES)
   The USRCLASS.DAT hive is located in the directory `C:\Users\<username>\AppData\Local\Microsoft\Windows`.

#### The Amcache Hive:

Apart from these files, there is another very important hive called the AmCache hive. This hive is located in `C:\Windows\AppCompat\Programs\Amcache.hve`. Windows creates this hive to save information on programs that were recently run on the system.

#### Transaction Logs and Backups:

The transaction logs can be considered as the journal of the changelog of the registry hive. This means that the transaction logs can often have the latest changes in the registry that haven't made their way to the registry hives. They stored as .LOG file in the same directory as the hive itself
For example, the transaction log for the SAM hive will be located in `C:\Windows\System32\Config` in the filename SAM.LOG.

Registry backups are the opposite of Transaction logs. These are the backups of the registry hives located in the `C:\Windows\System32\Config` directory. These hives are copied to the `C:\Windows\System32\Config\RegBack` directory every ten days. It might be an excellent place to look if you suspect that some registry keys might have been deleted/modified recently.

#### Questions:

1. What is the path for the five main registry hives, DEFAULT, SAM, SECURITY, SOFTWARE, and SYSTEM?
   **Answer:** C:\Windows\System32\Config
2. What is the path for the AmCache hive?
   **Answer:** C:\Windows\AppCompat\Programs\Amcache.hve

## Data Acquisition

For acquiring these files, we can use one of the following tools:

- Kape:
  [[KAPE]] is a live data acquisition and analysis tool which can be used to acquire registry data. It is primarily a command-line tool but also comes with a GUI. The below screenshot shows what the KAPE GUI looks like
- Autopsy:
  [[Autopsy]] gives you the option to acquire data from both live systems or from a disk image. After adding your data source, navigate to the location of the files you want to extract, then right-click and select the Extract File(s) option.
- FTK Imager:
  FTK Imager is similar to Autopsy and allows you to extract files from a disk image or a live system by mounting the said disk image or drive in FTK Imager. Below you can see the option to Export files as highlighted in the screenshot.

## Exploring Windows Registry

#### Registry Viewer:

As we can se in the screenshot below, AccessData's Registry Viewer has a similar user interface to the Windows Registry Editor. There are couple of limitations, though.!

#### Zimmerman's Registry Explorer:

Eric Zimmerman has developed a handful of [tools](https://ericzimmerman.github.io/#!index.md) that are very useful for performing Digital Forensics and Incident Response. **(DFIR)**  It also has a handy 'Bookmarks' option containing forensically important registry keys often sought by forensics investigators!

#### RegRipper:

[RegRipper](https://github.com/keydet89/RegRipper3.0) is a utility that takes a registry hive as input and outputs a report that extracts data from some of the forensically important keys and values in that hive. The output report is in a text file and shows all the results in sequential order.

## System Information and System Accounts

#### OS Version:

`SOFTWARE\Microsoft\Windows NT\CurrentVersion`

#### Current control set:

The hives containing the machine’s configuration data used for controlling system startup are called Control Sets. Commonly, we will see two Control Sets, ControlSet001 and ControlSet002, in the SYSTEM hive on a machine. In most cases (but not always), ControlSet001 will point to the Control Set that the machine booted with, and ControlSet002 will be the `last known good`  configuration. Their locations will be: `SYSTEM\ControlSet001` `SYSTEM\ControlSet002`
Windows creates a volatile Control Set when the machine is live, called the CurrentControlSet ( `HKLM\SYSTEM\CurrentControlSet` ). For getting the most accurate system information, this is the hive that we will refer to. We can find out which Control Set is being used as the CurrentControlSet by looking at the following registry value: `SYSTEM\Select\Current`
Similarly, the `last known good` configuration can be found using the following registry value:
`SYSTEM\Select\LastKnownGood`

#### Computer Name:

It is crucial to establish the Computer Name while performing forensic analysis to ensure that we are working on the machine we are supposed to work on. We can find the Computer Name from the following location:
`SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName`

#### Time Zone Information:

`SYSTEM`\CurrentControlSet\Control\TimeZoneInformation`

#### Network Interfaces and Past Networks:

`SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces`
Each Interface is represented with a unique identifier (GUID) subkey, which contains values relating to the interface’s TCP/IP configuration. This key will provide us with information like IP addresses, DHCP IP address and Subnet Mask, DNS Servers, and more. This information is significant because it helps you make sure that you are performing forensics on the machine that you are supposed to perform it on.

The past networks a given machine was connected to can be found in the following locations:
`SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Unmanaged`
`SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Managed`

#### Autostart Programs (Autoruns):

The following registry keys include information about programs or commands that run when a user logs on. 
`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Run`
`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\RunOnce`
`SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce`
`SOFTWARE\Microsoft\Windows\CurrentVersion\policies\Explorer\Run`
`SOFTWARE\Microsoft\Windows\CurrentVersion\Run`

The following registry key contains information about services:
`SYSTEM\CurrentControlSet\Services`

#### Sam hive and user information:

The SAM hive contains user account information, login information, and group information. This information is mainly located in the following location:
`SAM\Domains\Account\Users`

#### Questions

1. What is the Current Build Number of the machine whose data is being investigated?
   **Answer:** 19044
2. Which ControlSet contains the last known good configuration?
   **Answer:** 1
3. What is the Computer Name of the computer?
   **Answer:** THM-4n6
4. What is the value of the TimeZoneKeyName?
   **Answer:** Pakistan Standard Time
5. What is the DHCP IP address?
   **Answer:** 192.168.100.58
6. What is the RID of the Guest User account?
   **Answer:** 501

## Usage or knowledge of files/folders

#### Recent Files:

Windows maintains a list of recently opened files for each user. As we might have seen when using Windows Explorer, it shows us a list of recently used files.  This information is stored in the NTUSER hive and can be found on the following location:
`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`

Another interesting piece of information in this registry key is that there are different keys with file extensions, such as  `.pdf` , `.jpg` , `.docx`  etc. These keys provide us with information about the last used files of a specific file extension. So if we are looking specifically for the last used PDF files, we can look at the following registry key:
`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs\.pdf`

#### Office Recent Files:

Similar to the Recent Docs maintained by Windows Explorer, Microsoft Office also maintains a list of recently opened documents. This list is also located in the NTUSER hive. It can be found in the following location:
`NTUSER.DAT\Software\Microsoft\Office\VERSION`

The version number for each Microsoft Office release is different. An example registry key will look like this:
`NTUSER.DAT\Software\Microsoft\Office\15.0\Word`

Starting from Office 365, Microsoft now ties the location to the user's [live ID](https://www.microsoft.com/security/blog/2008/05/07/what-is-a-windows-live-id/) . In such a scenario, the recent files can be found at the following location. 
`NTUSER.DAT\Software\Microsoft\Office\VERSION\UserMRU\LiveID_####\FileMRU`

#### ShellBags

When any user opens a folder, it opens in a specific layout. Users can change this layout according to their preferences. These layouts can be different for different folders. This information about the Windows *'shell'*  is stored and can identify the Most Recently Used files and folders. Since this setting is different for each user, it is located in the user hives. We can find this information on the following locations:

`USRCLASS.DAT\Local Settings\Software\Microsoft\Windows\Shell\Bags`
`USRCLASS.DAT\Local Settings\Software\Microsoft\Windows\Shell\BagMRU`
`NTUSER.DAT\Software\Microsoft\Windows\Shell\BagMRU`
`NTUSER.DAT\Software\Microsoft\Windows\Shell\Bags`

#### Open/Save and LastVisited Dialog MRUs:

When we open or save a file, a dialog box appears asking us where to save or open that file from. It might be noticed that once we open/save a file at a specific location, Windows remembers that location. This implies that we can find out recently used files if we get our hands on this information. We can do so by examining the following registry keys
`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\OpenSavePIDlMRU`
`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\LastVisitedPidlMRU`

#### Windows Explorer Address/Search Bars:

Another way to identify a user's recent activity is by looking at the paths typed in the Windows Explorer address bar or searches performed using the following registry keys, respectively.
`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths`
`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery`
Here is how the TypedPaths key looks like in Registry Explorer:

#### Questions:

1. When was EZtools opened?
   **Answer:** 2021-12-01 13:00:34
2. At what time was My Computer last interacted with?
   **Answer:** 2021-12-01 13:06:47
3. What is the Absolute Path of the file opened using notepad.exe
   **Answer:** C:\Program Files\Amazon\Ec2ConfigService\Settings
4. When was this file opened?
   **Answer:** 2021-11-30 10:56:19

## Evidence of Execution

#### UserAssist:

Windows keeps track of applications launched by the user using Windows Explorer for statistical purposes in the User Assist registry keys. These keys contain information about the programs launched, the time of their launch, and the number of times they were executed. However, programs that were run using the command line can't be found in the User Assist keys. The User Assist key is present in the NTUSER hive, mapped to each user's GUID. We can find it at the following location:
`NTUSER.DAT\Software\Microsoft\Windows\Currentversion\Explorer\UserAssist\{GUID}\Count`

#### ShimCache:

ShimCache is a mechanism used to keep track of application compatibility with the OS and tracks all applications launched on the machine. Its main purpose in Windows is to ensure backward compatibility of applications. It is also called Application Compatibility Cache (AppCompatCache). It is located in the following location in the SYSTEM hive:
`SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache`

ShimCache stores file name, file size, and last modified time of the executables.
We can use the following command to run the AppCompatCache Parser Utility:
`AppCompatCacheParser.exe --csv <path to save output> -f <path to SYSTEM hive for data parsing> -c <control set to parse>`

#### AmCache:

The AmCache hive is an artifact related to ShimCache. This performs a similar function to ShimCache, and stores additional data related to program executions. This data includes execution path, installation, execution and deletion times, and SHA1 hashes of the executed programs. This hive is located in the file system at:
`C:\Windows\appcompat\Programs\Amcache.hve`
Information about the last executed programs can be found at the following location in the hive:
`Amcache.hve\Root\File\{Volume GUID}\`

#### BAM/DAM:

Background Activity Monitor or BAM keeps a tab on the activity of background applications. Similar Desktop Activity Moderator or DAM is a part of Microsoft Windows that optimizes the power consumption of the device. Both of these are a part of the Modern Standby system in Microsoft Windows.

In the Windows registry, the following locations contain information related to BAM and DAM. This location contains information about last run programs, their full paths, and last execution time.
`SYSTEM\CurrentControlSet\Services\bam\UserSettings\{SID}`
`SYSTEM\CurrentControlSet\Services\dam\UserSettings\{SID}`

#### Questions:

1. How many times was the File Explorer launched?
   **Answer:** 26
2. What is another name for ShimCache?
   **Answer:** AppCompatCache
3. Which of the artifacts also saves SHA1 hashes of the executed programs?
   **Answer:** AmCache
4. Which of the artifacts saves the full path of the executed programs?
   **Answer:** BAM/DAM

## External Devices/USB device forensics

#### Device identification:

The following locations keep track of USB keys plugged into a system. These locations store the vendor id, product id, and version of the USB device plugged in and can be used to identify unique devices. These locations also store the time the devices were plugged into the system.
`SYSTEM\CurrentControlSet\Enum\USBSTOR`
`SYSTEM\CurrentControlSet\Enum\USB`

#### First/Last Times:

Similarly, the following registry key tracks the first time the device was connected, the last time it was connected and the last time the device was removed from the system.
`SYSTEM\CurrentControlSet\Enum\USBSTOR\Ven_Prod_Version\USBSerial#\Properties\{83da6326-97a6-4088-9453-a19231573b29}\####`
In this key, the #### sign can be replaced by the following digits to get the required information:

- 0064 - First Connection time
- 0066 - Last Connection time
- 0067 - Last removal time

#### USB device Volume Name:

The device name of the connected drive can be found at the following location:
`SOFTWARE\Microsoft\Windows Portable Devices\Devices`

#### Questions:

1. What is the serial number of the device from the manufacturer 'Kingston'?
   **Answer:** 1C6f654E59A3B0C179D366AE&0
2. What is the name of this device?
   **Answer:** Kingston Data Traveler 2.0 USB Device
3. What is the friendly name of the device from the manufacturer 'Kingston'?
   **Answer:** USB

## Conclusion

You can learn more about Windows Forensics in our [[Windows Forensics 2]] room, where we cover even more exciting ways to perform forensics on a Windows machine, and the [[KAPE]] room to understand how to perform forensics in a quick and efficient manner.

## Cheatsheet!
