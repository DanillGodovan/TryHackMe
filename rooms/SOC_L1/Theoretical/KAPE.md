In this room, we will:

- Learn about KAPE
- How KAPE works
- The different targets and modules used by KAPE
- Collection and analysis of forensic data using KAPE

## Introduction to KAPE

Kroll Artifact Parser and Extractor **(KAPE)** parses and extracts Windows forensics artifacts.
It has 2 purposes:

- Collect files
- Process the collected files as per the provided options
  For that KAPE uses the concept of targets and modules. Targets can be defined as the forensic artifacts that need to be collected

#### How it works

KAPE is extensible and highly configurable. The collection of targets KAPE adds t he files to a queue an copies them in two passes. In the first pass it copies the files that it can. (Not locked by the OS). The rest of files are passed to a secondary queue. The 2nd pass uses bypass technique named raw disk head to copy the files. They are saved with original timestamps and metadata and stored in a similiar directory structure.
It is portable and can be used from network locations or USB drives.

#### Questions

1. From amongst kape.exe and gkape.exe, which binary is used to run GUI version of KAPE?
   **Answer:** gkape.exe

## Target Options

Targets are the artifacts that need to be collected from a szstem or image and copied to our provided destination. For example Windows Prefetch is a forensic artifact for evidence of execution so that we can create a Target for it. Targets copz files from one place to another.
The last four files at the bottom are guides and templates to create `Targets` and `Compound Targets` of our own. We will discuss `Compound Targets` later in this task. As you can see, the targets are grouped into different directories. Let's check out the `Windows` directory to see what we have:
We can see different .tkape extension files. This is how a `Target` is defined for KAPE. A TKAPE file contains information about the artifact that we want to collect, such as the path, category, and file masks to collect. As an example, below is how the Prefetch `Target` is defined
This TKAPE file tells KAPE to collect files with the file mask `*.pf` from the path `C:\Windows\prefetch` and `C:\Windows.old\prefetch`.

#### Compound Targets

KAPE also supports `Compound Targets`. These are `Targets` that are compounds of multiple other targets. As mentioned in the previous tasks, KAPE is often used for quick triage collection and analysis.
﻿Examples of `Compound Targets` include `!BasicCollection`, `!SANS_triage` and `KAPEtriage`. We can view the `Compound Targets` on the path `KAPE\Targets\Compound`. The following image shows what a `Compound Target` for evidence of execution looks like

#### !Disabled

This directory contains `Targets` that you want to keep in the KAPE instance but you don't want them to appear in the active Targets list.

#### !Local

If you have created some `Targets` that you don't want to sync with the KAPE Github repository, you can place them here.

#### Questions

1. What is the file extension for KAPE `Targets`?
   **Answer:** .tkape
2. What type of `Target` will we use if we want to collect multiple artifacts with a single command?
   **Answer:** Compound targets

## Module Options

Modules are running specific tools against provided set of files (aka Targets). Their goal is to run some command to store the output, the output is in the form of CST or TXT files.
Guides and Templates are the same as for Targets. Also !Disabled, !Local and Compound Directories which are similar we saw in the previous task. Most of the modules are grouped together in different directories. One thing we find different is the `bin` directory. The modules files have `.mkape` extension

#### The bin directory:

The `bin` directory contains executables that we want to run on the system but are not natively present. KAPE will run executables either from the `bin` directory or the complete path. An example is the EZTools from Eric Zimmerman.

#### Questions:

1. What is the file extension of the `Modules` files?
   **Answer:** .mkape
2. What is the name of the directory where binary files are stored, which may not be present on a typical system, but are required for a particular KAPE Module?
   **Answer:** bin

## KAPE GUI

It's the window of the gkape.exe. You can see Module and Targets options and check them to enable it
If we want perform forensics on the same machine on which KAPE is running, we will provide `C:\`
for Target source. We can select the target destination of our choice. All triage files will be copied to the Target Destination.

The flush option will delete all contents of the Target Destination.
Add %d will append date info to directory name where the collected data is saved. Similarly, Add %m will append machine info to Target Destination directory.

We can select if we want to process Volume Shadow Copies by enabling `Process VSCs` We can select the transfer checkbox if we want to transfer checkbox if we want to transfer the collected artifacts through an SFTP server or an S3 bucket. For transfer the files must be enclosed in a container, which can be ZIP, VHD or VHDX. Similarly we can provide exclusions based on SHA-1 and KAPE will not copy the excluded files.

In the current command line tab. We can see the command line options being added or removed while configuring the UI. This Windows will show more options in the command line as we add options.

When using both Target and Module Options, providing Module Source is not required. The selected Modules will use the Target destination as the source.

#### Questions:

1. In the second to last screenshot above, what target have we selected for collection?
   **Answer:** KapeTriage
2. In the second to last screenshot above, what module have we selected for processing?
   **Answer:** EZParser
3. What option has to be checked to append date and time information to triage folder name?
   **Answer:** %d
4. What option needs to be checked to add machine information to the triage folder name?
   **Answer:** %m

## KAPE CLI

For a list of all the different switches that can be used with KAPE, open an elevated PowerShell (Run As Administrator), go to the path where the KAPE binary is located, and type `kape.exe`. You will see something like this as an output.

```powershell
D:\KAPE>kape.exe

KAPE version 1.1.0.1 Author: Eric Zimmerman (kape@kroll.com)

tsource Target source drive to copy files from (C, D:, or F:\ for example)
target Target configuration to use
tdest Destination directory to copy files to. If --vhdx, --vhd or --zip is set, files will end up in VHD(X) container or zip file
tlist List available Targets. Use . for Targets directory or name of subdirectory under Targets.
tdetail Dump Target file details tflush Delete all files in 'tdest' prior to collection
tvars Provide a list of key:value pairs to be used for variable replacement in Targets. Ex: --tvars user:eric would allow for using %user% in a Target which is replaced with eric at runtime. Multiple pairs should be separated by ^
tdd Deduplicate files from --tsource (and VSCs, if enabled) based on SHA-1. First file found wins. Default is TRUE

msource Directory containing files to process. If using Targets and this is left blank, it will be set to --tdest automatically module Module configuration to use
mdest Destination directory to save output to mlist List available Modules. Use . for Modules directory or name of subdirectory under Modules.
mdetail Dump Module processors details mflush Delete all files in 'mdest' prior to running Modules
mvars Provide a list of key:value pairs to be used for variable replacement in Modules. Ex: --mvars foo:bar would allow for using %foo% in a module which is replaced with bar at runtime. Multiple pairs should be separated by ^
mef Export format (csv, html, json, etc.). Overrides what is in Module config
sim Do not actually copy files to --tdest. Default is FALSE
vss Process all Volume Shadow Copies that exist on --tsource. Default is FALSE
vhdx The base name of the VHDX file to create from --tdest. This should be an identifier, NOT a filename. Use this or --vhd or --zip
vhd The base name of the VHD file to create from --tdest. This should be an identifier, NOT a filename. Use this or --vhdx or --zip
zip The base name of the ZIP file to create from --tdest. This should be an identifier, NOT a filename. Use this or --vhdx or --vhd

scs SFTP server host/IP for transferring *compressed VHD(X)* container
scp SFTP server port. Default is 22
scu SFTP server username. Required when using --scs
scpw SFTP server password scd SFTP default directory to upload to. Will be created if it does not exist
scc Comment to include with transfer. Useful to include where a transfer came from. Defaults to the name of the machine where KAPE is running
s3p S3 provider name. Example: spAmazonS3 or spGoogleStorage. See 'https://bit.ly/34s9nS6' for list of providers. Default is 'spAmazonS3'
s3r S3 region name. Example: us-west-1 or ap-southeast-2. See 'https://bit.ly/3aNxXhc' for list of regions by provider
s3b S3 bucket name
s3k S3 Access key
s3s S3 Access secret
s3st S3 Session token
s3kp S3 Key prefix. When set, this value is used as the beginning of the key. Example: 'US1012/KapeData'
s3o When using 'spOracle' provider, , set this to the 'Object Storage Namespace' to use
s3c Comment to include with transfer. Useful to include where a transfer came from. Defaults to the name of the machine where KAPE is running

s3url S3 Presigned URL. Must be a PUT request vs. a GET request

asu Azure Storage SAS Uri
asc Comment to include with transfer. Useful to include where a transfer came from. Defaults to the name of the machine where KAPE is running

zv If true, the VHD(X) container will be zipped after creation. Default is TRUE
zm If true, directories in --mdest will be zipped. Default is FALSE
zpw If set, use this password when creating zip files (--zv | --zm | --zip)

hex Path to file containing SHA-1 hashes to exclude. Only files with hashes not found will be copied

debug Show debug information during processing
trace Show trace information during processing

gui If true, KAPE will not close the window it executes in when run from gkape. Default is FALSE

ul When using _kape.cli, when true, KAPE will execute entries in _kape.cli one at a time vs. in parallel. Default is FALSE

cu When using _kape.cli, if true, KAPE will delete _kape.cli and both Target/Module directories upon exiting. Default is FALSE

sftpc Path to config file defining SFTP server parameters, including port, users, etc. See documentation for examples
sftpu When true, show passwords in KAPE switches for connection when using --sftpc. Default is TRUE

rlc If true, local copy of transferred files will NOT be deleted after upload. Default is FALSE
guids KAPE will generate 10 GUIDs and exit. Useful when creating new Targets/Modules. Default is FALSE
sync If true, KAPE will download the latest Targets and Modules from specified URL prior to running. Default is https://github.com/EricZimmerman/KapeFiles/archive/master.zip

ifw If false, KAPE will warn if a process related to FTK is found, then exit. Set to true to ignore this warning and attempt to proceed. Default is FALSE


Variables:
%d = Timestamp (yyyyMMddTHHmmss)
%s = System drive letter
%m = Machine name
```

We can see from the above screenshot that while collecting Targets, the switches `tsource`, `target` and `tdest` are required. Similarly, when processing files using Modules, `module` and `mdest` are required switches. The other switches are optional as per the requirements of the collection.

With this information, let's build a command to perform the same task we performed in the previous task. i.e., collect triage data using the `KapeTriage` Compound Target and process it using the `!EZParser` Compound Module.

#### Batch Mode:

KAPE can also be run in batch mode. What this means is that we can provide a list of commands for KAPE to run in a file named `_kape.cli`. Then we keep this file in the directory containing the KAPE binary. When `kape.exe` is executed as an administrator, it checks if there is `_kape.cli` file present in the directory. If so, it executes the commands mentioned in the cli file. This mode can be used if you need someone to run KAPE for you, you will keep all the commands in a single line, and all you need is for the person to right-click and run kape.exe as administrator. For example, if we have to perform the same task as we did earlier in this task using batch mode, we will have to create a \_kape.cli file with the following content:

`--tsource C: --target KapeTriage --tdest C:\Users\thm-4n6\Desktop\Target --mdest C:\Users\thm-4n6\Desktop\module --module !EZParser`

#### Questions:

1. Run the command `kape.exe` in an elevated shell. Take a look at the different switches and variables. What variable adds the collection timestamp to the target destination?
   **Answer:** %d
2. What variable adds the machine information to the target destination?
   **Answer:** %m
3. Which switch can be used to show debug information during processing?
   **Answer:** debug
4. Which switch is used to list all targets available?
   **Answer:** tlist
5. Which flag, when used with batch mode, will delete the \_kape.cli, targets and modules files after the execution is complete?
   **Answer:** cu

## Hands-on Challenge

Organization X has an Acceptable Use Policy for their Portable Devices, including Laptops. This policy forbids users from connecting removable or Network drives, installing software from unknown locations, and connecting to unknown networks. It looks like one of the users has violated this policy. Can you help Organization X find out if the user violated the Acceptable Use Policy on their device? The user's machine is attached to the room as a VM.

Navigate to the KAPE directory placed on the Desktop in the attached VM. Run KAPE with your desired Target and Module options, and answer the following questions.

#### Questions

All questions are bound to registry and because of that only one folder play a huge interest for us - Registry

1. Two USB Mass Storage devices were attached to this Virtual Machine. One had a Serial Number  0123456789ABCDE. What is the Serial Number of the other USB Device?
   **Answer:** 1C6F654E59A3B0C179D366AE
2. 7zip, Google Chrome and Mozilla Firefox were installed from a Network drive location on the Virtual Machine. What was the drive letter and path of the directory from where these software were installed?
   **Answer:** Z:\Setups
3. What is the execution date and time of CHROMESETUP.EXE in MM/DD/YYYY HH:MM?
   **Answer:** 11/25/2021 03:33
4. What search query was run on the system?
   **Answer:** RunWallpaperSetup.cmd
5. When was the network named Network 3 First connected to?
   **Answer:** 11/30/2021 15:44
6. KAPE was copied from a removable drive. Can you find out what was the drive letter of the drive where KAPE was copied from?
   **Answer:** E:
