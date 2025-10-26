## Welcome to the team, kid. I have something for you to get your feet wet.

#### Our client has a newly hired employee who saw a suspicious-looking janitor exiting his office as he was about to return from lunch.

I want you to investigate if there was user activity while the user was away **between** **12:05 PM to 12:45 PM on the 19th of November 2022**. If there are, figure out what files were accessed and exfiltrated externally.

You'll be accessing a live system, but use the disk image already exported to the `C:\Users\THM-RFedora\Desktop\kape-results\C` directory for your investigation. The link to the tools that you'll need is in `C:\Users\THM-RFedora\Desktop\tools` ([[KAPE]])

Finally, I want to remind you that you signed an NDA, so avoid viewing any files classified as top secret. I don't want us to get into trouble.

## Prerequisites

[[Windows Forensics 1]]
[[Windows Forensics 2]]

## Snooping around

Initial investigations reveal that someone accessed the user's computer during the previously specified timeframe.

Whoever this someone is, it is evident they already know what to search for. Hmm. Curious.

#### Questions:

1. What file type was searched for using the search bar in Windows Explorer?
   From the KAPE extracted disk image we search for NTUSER.dat and upload it to Registry Explorer with both transaction logs .LOG1 and .LOG2
   Then we go to the path Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery
   Scroll through queries
   **Answer:** .pdf
2. What top-secret keyword was searched for using the search bar in Windows Explorer?
   Looking further we see the answer
   **Answer:** continental

## Can't simply open it

Not surprisingly, they quickly found what they are looking for in a matter of minutes.

Ha! They seem to have hit a snag! They needed something first before they could continue.

**Note:**  When using the [[Autopsy]] Tool, you can speed up the load times by only selecting "Recent Activity" when configuring the Ingest settings.

First of all we need to generate Autopsy data source

1. Set up the project, give it a name and path where it will be saved
2. Add Data Source
3. Select Logical files
4. Choose "C" folder from [[KAPE]] analysis
5. Deselect all except Recent Activity mark
   Continue to questions.

#### Questions:

1. What is the name of the downloaded file to the Downloads folder?
   **Answer:** Go to the Web Downloads folder and sort all by Download time
   Also it was **7z2201-x64.exe**
2. When was the file from the previous question downloaded? (YYYY-MM-DD HH:MM:SS UTC)
   **Answer:** Check next graphs after Path!
   **2022-11-19 12:09:19 UTC**
3. Thanks to the previously downloaded file, a PNG file was opened. When was this file opened? (YYYY-MM-DD HH:MM:SS)
   From the Web Downloads we can see continental.7z file that was download afterwards. Go to Recent Documents folder to see that continental.png was the file opened on the victim machine. Although we can't see the time when it was executed, we can parse it through EZTools such as PECmd, JLECmd. ([[Windows Forensics 2]]). There was no Recent for PECmd, used JLECmd and path was `C:\Users\THM-RFedora\Desktop\kape-results\C\Users\THM-RFedora\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations`
   Afterwards check the file with EZViewer. Results are showing in SourceCreated tab.
   Also the answer is: 2022-11-19 12:10:21

## Sending it outside

Uh oh. They've hit the jackpot and are now preparing to exfiltrate data outside the network.
There is no way to do it via USB. So what's their other option?

#### Questions:

1. A text file was created in the Desktop folder. How many times was this file opened?
   **Answer:** We look again at Recent Documents tab and see only one .txt file used in Desktop folder - launchcode.txt. We filter by path and get the answer - 2
2. When was the text file from the previous question last modified? (MM/DD/YYYY HH:MM)
   **Answer:** We can see it again in Jumplist dump we created. Look at LastModified tab and answer the question - 11/19/2022 12:12
3. The contents of the file were exfiltrated to pastebin.com. What is the generated URL of the exfiltrated data?
   **Answer:** We need to search for "pastebin" word in a substring keyword search in Web History Tab on [[Autopsy]]. - https://pastebin.com/1FQASAav\
4. What is the string that was copied to the pastebin URL?
   **Answer:** Follow the link and paste string from there to the answer field - ne7AIRhi3PdESy9RnOrN

## Conclusion

At this point, we already have a good idea of what happened. The malicious threat actor was able to successfully find and exfiltrate data. While we could not determine who this person is, it is clear that they knew what they wanted and how to get it.

I wonder what's so important that they risked accessing the machine in-person... I guess we'll never know.

Anyways, you did good, kid. I guess it was too easy for you, huh?
