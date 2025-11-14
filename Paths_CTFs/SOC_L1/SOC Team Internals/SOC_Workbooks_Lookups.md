#### Learning Objectives

- Familiarise yourself with SOC investigation workbooks
- Learn where to find and how to use asset inventory in SOC
- Understand the importance of corporate network diagrams
- Practice workflow building inside an interactive interface

#### Prerequisites

[[SOC L1 Alert Reporting]]
[[SOC L1 Alert Triage]]

## Assets & Identities

To understand what each identity responsible for, we need to have an Identity Inventory. It's a catalogue of corporate employees (user accounts), services and their details like privileges, contacts and roles within the company.

| Solution         | Examples               | Description                                                           |
| ---------------- | ---------------------- | --------------------------------------------------------------------- |
| Active Directory | On-prem AD, Entra ID   | AD itself is an identity database, and it is commonly used by SOC     |
| SSO Providers    | Okta, Google Workspace | Cloud alternative for AD, an easy way to manage and search the users  |
| HR Systems       | BambooHR, SAP, HiBob   | Limited to employees only, but usually provides full employee data    |
| Custom Solution  | CSV or Excel Sheets    | It is common for IT or security teams to maintain their own solutions |

#### Asset Inventory

is a list of all computing resources within an organisation's IT environment. Asset can refer to software, hardware or employees.

| Solution         | Examples             | Description                                                           |
| ---------------- | -------------------- | --------------------------------------------------------------------- |
| Active Directory | On-prem AD, Entra ID | AD is not only an identity but also a solid asset inventory database  |
| SIEM or EDR      | Elastic, CrowdStrike | Some SIEM or EDR agents collect information about the monitored hosts |
| MDM Solution     | MS Intune, Jamf MDM  | A dedicated class of solutions created to list and manage assets      |
| Custom Solution  | CSV or Excel Sheets  | Same as with the identity inventory, custom solutions are common      |

#### Questions

1. Looking at the identity inventory, what is the role of R.Lund at the company?
   **Answer:** US Financial Adviser
2. Checking the asset inventory, what data does the HQ-FINFS-02 server store?
   **Answer:** financial records
3. Finally, does the file sharing from the scenario look legitimate and expected? (Yea/Nay)
   **Answer:** Yea

## Network Diagrams

Continuing identity and asset inventory topics, you might also need to look at the alert from a network point of view, especially in bigger companies. If you investigate a multiple alerts based on firewall logs you may need to give a meaning on IPs you see:

1. **08:00**: An IP **103.61.240.174** is repeatedly connecting to a corporate firewall via port **TCP/10443**
2. **08:23**: Firewall logs show that the IP 103.61.240.174 was translated to an internal **10.10.0.53** IP
3. **08:25**: The IP 10.10.0.53 is scanning the **172.16.15.0/24** network range but does not find open ports
4. **08:32**: The same IP is now scanning the **172.16.23.0/24** network range, and the attack seems to be ongoing

#### Network Diagrams

A **network diagram**, a visual schema presenting existing locations, subnets, and their connections, is an answer to your questions:![[Pasted image 20251109200011.png]]
Depending on a company's size and structure, you may see more complex diagrams, but their use for SOC analysts remains the same - to help understand suspicious network activity. In our scenario, you can refer to the network diagram and reconstruct the attack path as follows:

1. Threat actor behind the 103.61.240.174 IP performed **VPN brute force**, targeting vpn.tryhatme.thm
2. After a successful brute force and VPN login, the threat actor was assigned an IP from the **VPN Subnet**
3. Then, the adversary tried to scan the **Database Subnet**, but was likely blocked by the firewall rules
4. Seeing no success, the threat actor switches to the **Office Subnet**, looking for their next target

#### Questions

1. According to the network diagram, which service is exposed on the TCP/10443 port?
   **Answer:** VPN
2. Now, which subnet would the server behind 172.16.15.99 IP belong to?
   **Answer:** Database Subnet
3. Finally, does the scenario look like a True Positive (TP) or False Positive (FP)?
   **Answer:** TP

## Workbooks Theory

**SOC workbook**, also called playbook, runbook, or workflow, is a structured document that defines the steps required to investigate and remediate specific threats efficiently and consistently. Since L1 analysts are considered junior specialists and are not expected to triage every possible attack scenario perfectly, senior analysts often prepare workbooks to support their less experienced teammates. L1 analysts are recommended and sometimes even required to triage the alerts precisely according to workbooks to avoid mistakes and streamline the analysis. Example:
![[Pasted image 20251109200316.png]]By following the steps in the correct order, you can guarantee high-quality alert triage and eliminate cases where the verdict is made without enough evidence:

1. **Enrichment**: Use Threat Intelligence and identity inventory to get information about the affected user
2. **Investigation**: Using the gathered data and SIEM logs, make your verdict if the login is expected
3. **Escalation**: Escalate the alert to L2 or communicate the login with the user if necessary

#### Questions

1. Which SOC role would use workbooks the most (e.g. SOC Manager)?
   **Answer:** SOC L1 Analyst
2. What is the process of gathering user, host, or IP context using TI and lookups?
   **Answer:** Enrichment
3. Looking at the workbook example, what platform is used as an identity inventory source?
   **Answer:** BambooHR

## Workbooks Practice

#### Questions:

1. What flag did you receive after completing the first workbook?
   **Answer:** THM{the_most_common_soc_workbook}
2. What flag did you receive after completing the second workbook?
   **Answer:** THM{be_vigilant_with_powershell}
3. What flag did you receive after completing the third workbook?
   **Answer:** THM{asset_inventory_is_essential}
