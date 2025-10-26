As a SOC analyst, it’s important to be able to investigate different types of suspicious activity across a variety of assets in the environment. Knowing what to look for and which details matter most during an investigation is a key part of the role.

Learning Objectives
Learn how to properly investigate alerts in a SOC environment.
Understand how to investigate brute-force attacks on Linux systems.
Discover the persistence mechanism on Windows systems.
Analyse a web shell on a vulnerable web server.
Learn how to investigate alerts for three given scenarios using Splunk.

## Prerequisites

- [[Splunk Basics]]
- [[Incident handling with Splunk]]
- [[Investigating with Splunk]]

## Initial Access Alert

#### Alert Scenario

You’ve just started your first shift as a SOC analyst at an MSSP. Only a few minutes have passed since an alert about a possible brute force attack appeared on the platform.

Alert Details:
Alert Name: Brute Force Activity Detection
Time: 17/09/2025 9:00:21 AM
Target Host: tryhackme-2404
Source IP: 10.10.242.248
Your job is to investigate this activity and decide whether it should be considered suspicious.

From the details we can consider two fields of interest: Target Host Time and Source IP. We can seed that the source IP is already internal organization IP what suggests adversaries being inside the network or VPN. Based on Hostname alone it's hard to say which machine that exactly was.

Let's see with this prompt possible Brute-Force attempts:

```
index="linux-alert" sourcetype="linux_secure" 10.10.242.248
| search "Accepted password for" OR "Failed password for" OR "Invalid user"
| sort + _time
```

The first thing we notice is a large number of events happened on this IP address, moreover most of accounts are non existent in organisation.
So far, we haven’t found any signs of brute force. Therefore, let’s run another search to see the number of login attempts made for each user.

````
```index="linux-alert" sourcetype="linux_secure" 10.10.242.248
| rex field=_raw "^\d{4}-\d{2}-\d{2}T[^\s]+\s+(?<log_hostname>\S+)"
| rex field=_raw "sshd\[\d+\]:\s*(?<action>Failed|Accepted)\s+\S+\s+for(?: invalid user)? (?<username>\S+) from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| eval process="sshd"
| stats count values(src_ip) as src_ip values(log_hostname) as hostname values(process) as process by username
````

By using that prompt we clearly see that the most of log in activities are done to john.smith account which may be indicator of brute-force.
Although we see that there are brute-force attempts, we need to see if any of them was successful:

```
index="linux-alert" sourcetype="linux_secure" 10.10.242.248
| rex field=_raw "^\d{4}-\d{2}-\d{2}T[^\s]+\s+(?<log_hostname>\S+)"
| rex field=_raw "sshd\[\d+\]:\s*(?<action>Failed|Accepted)\s+\S+\s+for(?: invalid user)? (?<username>\S+) from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| eval process="sshd"
| stats count values(action) values(src_ip) as src_ip values(log_hostname) as hostname values(process) as process  by username
```

From the results we see that here was the successful action for john.smith

At this point, your investigation of this activity as a SOC Level 1 analyst comes to an end, since your responsibility is to identify malicious activity and escalate it to the Level 2 analyst. Therefore, let’s now discuss what will happen next and what questions still remain for the SOC team regarding this case.

Why did the attacker have a local IP address? Could it be that they are already inside our network? If so, for how long?
How did the attacker obtain information about the users, specifically their usernames?
What happened after the attacker gained access to the tryhackme-2404 host?

#### Questions:

1. How many failed login attempts were made on the user john.smith?
   **Answer:** from the given RegExp you need remove accepted attempts -> 500
2. What was the duration of the brute force attack in minutes?
   **Answer:** Check the time between first from this 500 and last -> 5
3. What username was the attacker able to privilege escalate to?
   **Answer:** We need to swap source types to linux-secure and seek for specific opened sessions -> root
4. What is the name of the user account created by the attacker for persistence?
   **Answer:** in this source check for creation of new session -> system-utm

## Persistence Alert

#### Alert Scenario

You are working as a Level 1 SOC Analyst on shift at an MSSP. An alert has come through indicating that a suspicious scheduled task was created on a host.  
**Alert Details:**

- **Alert Name:** Potential Task Scheduler Persistence Identified
- **Time:** 30/08/2025 10:06:07 AM
- **Host:** WIN-H015
- **User:** oliver.thompson
- **Task Name:** AssessmentTaskOne
  Your job is to investigate this activity and decide whether it should be considered suspicious.
  -> Supplied index

#### Investigating the Alert

Let's focus on Host and User. We need to understand what kind of host that is: workstation or server. This context is key before continuing. Many organizations have an Asset Inventory for this.

Servers often use prefixes like **SRV**, **WEB**, **MSQL**, while workstations are labelled **WIN** or **HOST**. In our case, **WIN-H015** suggests it’s a workstation.

Next we check the User. Use the identity table to see their role. This helps us to decide if the activity fits their job. For example it will be strange if HR will create tasks, but normal for IT Staff. We can also check the user's location and working hours to see if the timing makes sense. In our case oliver.thompson is a System Engineer.

To start, we’ll query the task name `**AssessmentTaskOne**` along with event ID `**4698**`
Since we also know the exact time of activity, we can filter by timestamp to speed up the search. Note: For now we won't filter by host. By doing that we can check whether the activity is isolated to a single machine across multiple hosts

```
index="win-alert" EventCode=4698 AssessmentTaskOne
| table _time EventCode user_name host Task_Name Message
```

From our search, we can see that there’s only a single event related to this activity.Now, let’s look at the **Message** field to understand what this task actually does. Let’s go step by step, starting with the **Triggers** section.
We can see that the task runs every day at the same time on a user workstation, which is quite unusual. Let’s continue by analysing the **Message** field, focusing on the **Exec** and **Principals** sections, to see what task is being executed and under which user account. At this point, we can already see the first signs of malicious activity. This task will use `**certutil**` to download `**rv.exe**` from the **tryhotme** domain into the Temp folder under the name `**DataCollector.exe**`. It will then launch this file using a `**Start-Process**` PowerShell command. All of this activity will be executed under the user **oliver.thompson**. This is a clear example of persistence.  
What else can you find out? Try looking up the domain in Threat Intelligence platforms, it might be linked to a known attacker infrastructure. We classified this activity as a **True Positive**. Also, this activity should be escalated to an L2 analyst for deeper investigation.

#### Next Investigation Steps

Of course, there are still open questions, such as:

- How was this scheduled task created?
- How did the attacker gain access to the WIN-H015 host?
- How was the oliver.thompson account compromised?

#### Questions

1. What is the ProcessId of the process that created this malicious task?
   **Answer:** Take a closer look at the scheduled task event. At the end we could see ProcessId: **5816**
2. What is the name of the parent process for the process that created this malicious task?
   **Answer:** take a look at ParentImage -> cmd.exe
3. Which local group did the attacker enumerate during discovery?
   **Answer:** We need to sort for actions that was done in cmd.exe beforehand.
   `index="win-alert" cmd.exe ParentProcessId=4128`
   We see only this command - net localgroup Administrators
   also the answer is **Administrators**
4. What is the name of the workstation from which the Threat Actor logged into this host?
   **Answer:** First of all we need to sort for Event ID 4624 which means user logging on. Moreover it happened before searching for local groups and bound with oliver.thompson.
   `index="win-alert" EventCode=4624 oliver.thompson`
   First results do not include Workstation names but the next one most suspicious - DEV-QA-SERVER

## Possible Web Shell Alert

#### Alert Scenario

Your shift as an L1 SOC analyst continues, and you’ve now received the next alert that needs to be investigated. This time, the activity is related to the web.  
**Alert Details:**

- **Alert Name:** Potential Web Shell Upload Detected
- **Time:** 14/09/2025 09:31:51 AM
- **Resource:** http://web.trywinme.thm
- **Suspicious IP:** 171.251.232.40
  Your job is to investigate this activity and decide whether it should be considered suspicious.
  The logs for this task are located in the Splunk index win-web. Use the following query: `index=web-alert`

#### Investigating the Alert

The first thing of interest is the resource where the activity occurred, in this case `http://web.tryinme.thm`, which is the organisation's website hosted on the web server. The next point is the Suspicious IP address. We can check it across Threat Intelligence platforms to gather more information. Let's use AbuselPDB for this. As we can see this IP address has been flagged as malicious more than 3000 times.Now, let’s move into the SIEM to examine the specific activity carried out by this IP address.

```
index=web-alert 171.251.232.40
| table _time clientip useragent uri_path method status
| sort + _time
```

Right from it we can detect a large number of requests associated with this IP. What's interested is that the User Agent is set to Hydra, a popular tool commonly used by attackers to perform brute force attempts. In our case it's wp-login.php page. This is already a clear indicator of malicious activity. Let's take a closer look.
We excluded Hydra as the User-Agent from our search and reviewed the results.

```
index=web-alert 171.251.232.40 useragent!="Mozilla/5.0 (Hydra)"
| table  _time clientip useragent uri_path referer referer_domain method status
```

When reviewing the search results, one detail immediately stands out. A POST request was observer for admin-ajax.php with a referer pointing to **theme-editor.php?file=b374k.php**.. this is unusual because the theme editor should not reference any arbitrary .php files. The presence of **file=b374k.php** strongly suggests that the attacker may have uploaded or is interacting with a web shell. Let's stake a closer look at the logs related to b374k.php

```
index=web-alert 171.251.232.40 b374k.php
| table _time clientip useragent uri_path referer referer_domain method status
| sort + _time
```

We detected that the threat actor successfully gained access to a possible web shell file, **b374k.php**. After that, they began executing activity through it, specifically, we observed four successful **POST** requests. Unfortunately, the logs do not show how the attacker initially uploaded the web shell to the server
Sometimes attackers use popular web shells without even changing their filenames. Let’s do some research on the web shell we’ve identified. Just Google: "b374k.php web shell". We need to consider it as a True Positive and escalate it to a SOC Level 2 Analyst.

#### Next Investigation Steps

These questions should be reviewed by the SOC Level 2 analyst:

- Was the brute force attack using Hydra successful?
- How did the attacker upload the web shell to the server, given that SOC L1 did not identify any traces of the upload?
- What specific actions did the attacker perform on the server using commands through the web shell?
  After this, containment and remediation actions should take place

#### Questions:

1. What time did the brute-force activity using Hydra begin?
   Answer Format Example: 2025-01-15 12:30:45
   **Answer:** 2025-09-14 21:20:27
   The way to solve is to sort all with help of useragent and by time.

```
index=web-alert 171.251.232.40 useragent="Mozilla/5.0 (Hydra)"
| table  _time clientip useragent uri_path referer referer_domain method status
| sort + _time
```

2. Which user agent did the attacker use when interacting with the web shell?
   **Answer:** use the last given query and look for the user-agent section.
   Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36

```
index=web-alert 171.251.232.40 b374k.php
|table _time clientip useragent uri_path referer referer_domain method status
| sort + _time
```

3. What was the number of requests made by the attacker to the server via the web shell?
   **Answer:** look at the count of requests where referer is b374k.php - 4

## Conclusion

Great job completing this room! You've now gained practical experience in investigating different types of alerts you can encounter in the real world.

- Detecting anomalies on Windows and Linux systems.
- Analysing web shell activity and identifying its traces.
  More exciting challenges await you next!
