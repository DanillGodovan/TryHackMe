## Introduction

In this room, we will look at various tools that will aid us in analyzing phishing emails. We will:

- Look at tools that will aid us in examining email header information.
- Cover techniques to obtain hyperlinks in emails, expand the URLs if they're URL shortened.
- Look into tools to give us information about potentially malicious links without directly interacting with a malicious link.
- Cover techniques to obtain malicious attachments from phishing emails and use malware sandboxes to detonate the attachments to understand further what the attachment was designed to do.

## What Information should we collect?

Below is a checklist of the pertinent information an analyst (you) is to collect from the email header:

- Sender email address
- Sender IP address
- Reverse lookup of the sender IP address
- Email subject line
- Recipient email address (this information might be in the CC/BCC field)
- Reply-to email address (if any)
- Date/time
  Below is a checklist of the artifacts an analyst (you) needs to collect from the email body:
- Any URL links (if an URL shortener service was used, then we'll need to obtain the real URL link)
- The name of the attachment
- The hash value of the attachment (hash type MD5 or SHA256, preferably the latter)

## Email header analysis

For that purpose we can use Messageheader from the Google Admin Toolbox.
It analyses SMTP message headers which help identity the root cause of delivery delays.

- **Messageheader**: [https://toolbox.googleapps.com/apps/messageheader/analyzeheader](https://toolbox.googleapps.com/apps/messageheader/analyzeheader)
  Another tool is called **Message Header Analyzer**.
- **Message Header Analyzer**: [https://mha.azurewebsites.net/](https://mha.azurewebsites.net/)
  The tools below can help you analyze information about the sender's IP address:
- IPinfo.io: [https://ipinfo.io/](https://ipinfo.io/)
- URLScan.io: [https://urlscan.io/](https://urlscan.io/)
- Talos Reputation Center: [https://talosintelligence.com/reputation](https://talosintelligence.com/reputation)

#### Questions

1. What is the official site name of the bank that capitai-one.com tried to resemble?
   **Answer:** capitalone.com

## Email body analysis

Links can be extracted manually, either directly from an HTML formatted email or by sifting through the raw email header.
Below is an example of obtaining a link manually from an email by right-clicking the link and choosing **Copy Link Location**.
The same can be accomplished with the assistance of a tool. One tool that can aid us with this task is URL Extractor.

- URL Extractor: [https://www.convertcsv.com/url-extractor.htm](https://www.convertcsv.com/url-extractor.htm)
  After you have obtained the attachment, you can then get its hash. You can check the file's reputation with the hash to see if it's a known malicious document. There are many tools available to help us with this, but we'll focus on two primarily; they are listed below:
- Talos File Reputation: [https://talosintelligence.com/talos_file_reputation](https://talosintelligence.com/talos_file_reputation)
- VirusTotal

#### Questions

1. How can you manually get the location of a hyperlink?
   **Answer:** Copy Link Location

## Malware Sandbox

For instance, we can upload an attachment we obtained from a potentially malicious email and see what URLs it attempts to communicate with, what additional payloads are downloaded to the endpoint, persistence mechanisms, Indicators of Compromise (IOCs), etc. 
Some of these online malware sandboxes are listed below.

- Any.Run: [https://app.any.run/](https://app.any.run/)
- Hybrid Analysis: [https://www.hybrid-analysis.com/](https://www.hybrid-analysis.com/)
- [https://www.joesecurity.org/](https://www.joesecurity.org/)

## PhishTool

Per the site, "_Be you a security researcher investigating a new phish-kit, a SOC analyst responding to user reported phishing, a threat intelligence analyst collecting phishing IoCs or an investigator dealing with email-born fraud._

_PhishTool combines threat intelligence, OSINT, email metadata and battle tested auto-analysis pathways into one powerful phishing response platform. Making you and your organisation a formidable adversary - immune to phishing campaigns that those with lesser email security capabilities fall victim to._"

PhishTool conveniently grabs all the pertinent information we'll need regarding the email.

- Email sender
- Email recipient (in this case, a long list of CCed email addresses)
- Timestamp
- Originating IP and Reverse DNS lookup
  We can obtain information about the SMTP relays, specific X-header information, and IP info information.

We can automatically get feedback from VirusTotal since our community edition API key is connected.

#### Questions

1. Look at the Strings output. What is the name of the EXE file?
   **Answer:** \#454326_PDF.exe

## Phishing Case 1

#### Questions

1. What brand was this email tailored to impersonate?
   **Answer:** Netflix
2. What is the From email address?
   **Answer:** NetfIix\<JGQ47wazXe1xYVBrkeDg-JOg7ODDQwWdR@JOg7ODDQwWdR-yVkCaBkTNp.gogolecloud.com>\_\_\_\_\_\_\_\_\.\_\_\_\_\_\_\_\_\_\_\_\.\_\_\_\_\_\_
3. What is the originating IP? Defang the IP address.
   **Answer:** 209\[.]85\[.]167\[.]226
4. From what you can gather, what do you think will be a domain of interest? Defang the domain.
   **Answer:** etekno\[.]xyz
5. What is the shortened URL? Defang the URL.
   **Answer:** hxxps\[://]t\[.]co/yuxfZm8KPg?amp\==1

## Phishing Case 2

#### Questions

1. What does AnyRun classify this email as?
   **Answer:** Suspicious Activity
2. What is the name of the PDF file?
   **Answer:** Payment-updateid.pdf
3. What is the SHA 256 hash for the PDF file?
   **Answer:** cc6f1a04b10bcb168aeec8d870b97bd7c20fc161e8310b5bce1af8ed420e2c24
4. What two IP addresses are classified as malicious? Defang the IP addresses. (answer: **IP_ADDR,IP_ADDR**)
   **Answer:** 2[.]16[.]107[.]24,2[.]16[.]107[.]83
5. What Windows process was flagged as **Potentially Bad Traffic**?
   **Answer:** svchost.exe

## Phishing Case 3

1. What is this analysis classified as?
   **Answer:** Malicious Activity
2. What is the name of the Excel file?
   **Answer:** CBJ200620039539.xlsx
3. What is the SHA 256 hash for the file?
   **Answer:** 5f94a66e0ce78d17afc2dd27fc17b44b3ffc13ac5f42d3ad6a5dcfb36715f3eb
4. What domains are listed as malicious? Defang the URLs & submit answers in alphabetical order. (answer: **URL1,URL2,URL3**)
   **Answer:** biz9holdings[.]com,findresults[.]site,ww38[.]findresults[.]site
5. What IP addresses are listed as malicious? Defang the IP addresses & submit answers from lowest to highest. (answer: **IP1,IP2,IP3**)
   **Answer:** 75[.]2[.]11[.]242,103[.]224[.]182[.]251,204[.]11[.]56[.]48
6. What vulnerability does this malicious attachment attempt to exploit?
   **Answer:** CVE-2017-11882
