#### Learning Objectives

- Understand the need for SOC alert reporting and escalation
- Learn how to write alert comments or case reports properly
- Explore escalation methods and communication best practices
- Apply the knowledge to triage alerts in a simulated environment
- Feel more confident in [SOC Simulator](https://tryhackme.com/soc-sim) and during [SAL1](https://tryhackme.com/certification/security-analyst-level-1/details) certification

#### Prerequisites

[[SOC L1 Alert Triage]]

## Alert Funnel

![[Pasted image 20251108160801.png]]

## Alert Reporting

Before closing or passing the alert to L2, you might have to report it. Depending on team standards and alert severity, instead of a short alert comment, you can be required to document your investigation in detail, ensuring all relevant evidence is included. This is especially important for True Positives, which require escalation.

## Alert Escalation

If the True Positive alert requires additional actions or deeper investigation, escalate it to the L2 analyst for further review following the agreed procedures. That's where your alert report comes in handy since L2 will use it to get the initial context and spend less on the analysis from scratch.

## Communication

You may also need to communicate with other departments during or after the analysis. For example, ask the IT team if they confirm granting administrative privileges to some users or contact HR to get more information about the newly hired employee.

#### Questions

1. What is the process of passing suspicious alerts to an L2 analyst for review?
   **Answer:** Alert Escalation
2. What is the process of formally describing alert details and findings?
   **Answer:** Alert Reporting

## Reporting Guide

In every Report we need to include 5 Ws:

- **Who**: Which user logs in, runs the command, or downloads the file
- **What**: What exact action or event sequence was performed
- **When**: When exactly did the suspicious activity start and ended
- **Where**: Which device, IP, or website was involved in the alert
- **Why**: The most important W, the reasoning for your final verdict
  Also we need to differentiate between different purposes of Alert Report:
  **Provide context for escalation:** - A well-written report saves lots of time for L2 analyst - Also, it helps them quickly understand what happened|
  **Save findings for the records:** - Raw SIEM logs are stored for 3-12 months, but alerts are kept indefinitely<br>- As a result, it's better to keep all the context inside the alert, just in case|
  **Improve investigation skills:** - If you can't explain it simply, you don't understand it well enough - Report writing is a great way to boost L1 skills by summarising alerts|

#### Questions

1. According to the [SOC dashboard](https://static-labs.tryhackme.cloud/apps/socl1-alertreporting/), which user email leaked the sensitive document?
   **Answer:** m.boslan@tryhackme.thm
2. Looking at the new alerts, who is the "sender" of the suspicious, likely phishing email?
   **Answer:** support@microsoft.com
3. Open the phishing alert, read its details, and try to understand the activity.  
   Using the Five Ws template, what flag did you receive after writing a good report?
   **Answer:** THM{nice_attempt_faking_microsoft_support}

## Escalation Guide

You should escalate the alerts if:

1. The alert is an indicator of a major cyberattack requiring deeper investigation or DFIR
2. Remediation actions like malware removal, host isolation, or password reset are required
3. Communication with customers, partners, management, or law enforcement agencies is required
4. You just do not fully understand the alert and need some help from more senior analysts

#### Escalation Steps

To escalate the alert you need to reassign it to the L2 on shift and mention them in corporate chat or in person. Also you need to create sometimes formal written escalation request.
![L1 escalates phishing alert to L2, and L2 rotates user's credentials](https://tryhackme-images.s3.amazonaws.com/user-uploads/678ecc92c80aa206339f0f23/room-content/678ecc92c80aa206339f0f23-1743520297119.svg)

## Requesting L2 Support

It is generally fine for L1 to request senior support if something is unclear. Especially in your first months, it's always better to discuss the alert and clarify SOC procedures than to blindly close the alert you don't understand yourself. The procedures for requesting support may differ, but the flow generally looks like this:

#### Questions

1. Who is your current L2 in the [SOC dashboard](https://static-labs.tryhackme.cloud/apps/socl1-alertreporting/) that you can assign (escalate) the alerts to?
   **Answer:** E.Fleming
2. What flag did you receive after correctly escalating the alert from the previous task to L2?
   **Note:** If you correctly escalated the alert earlier, just edit the alert and click "Save" again
   **Answer:** THM{good_job_escalating_your_first_alert}
3. Now, investigate the second new alert in the queue and provide a detailed alert comment.  
   Then, decide if you need to escalate this alert and move on according to the process.  
   After you finish your triage, you should receive a flag, which is your answer!
   **Answer:** THM{looks_like_webshell_via_old_exchange}

## SOC Communication

#### Communication Cases

- **You need to escalate an urgent, critical alert, but L2 is unavailable and does not respond for 30 minutes.**  
   Ensure you know where to find emergency contacts. First, try to call L2, then L3, and finally your manager.
- **The alert about Slack/Teams account compromise requires you to validate the login with the affected user.**  
   Do not contact the user through the breached chat - use alternative contact methods like a phone call.
- **You receive an overwhelming number of alerts during a short period of time, some of which are critical.**  
   Prioritise the alerts according to the workflow, but inform your L2 on shift about the situation.
- **After a few days, you realise that you misclassified the alert and likely missed a malicious action.**  
   Immediately reach out to your L2 explaining your concerns. Threat actors can be silent for weeks before impact.
- **You can not complete the alert triage since the SIEM logs are not parsed correctly or are not searchable.**  
   Do not skip the alert - investigate what you can and report the issue to your L2 on shift or SOC engineer.

#### Questions

1. Should you first try to contact your manager in case of a critical threat (Yea/Nay)?
   **Answer:** Nay
2. Should you immediately contact your L2 if you think you missed the attack (Yea/Nay)?
   **Answer:** Yea
