## Prerequisites

- Complete the [[Junior Security Analyst]] room
- Complete the [[SOC Role in Blue Team]] room

#### Learning Objectives

- Learn the role of a system in a modern digital world
- Explore a variety of real-world attacks targeting systems
- Practice the acquired knowledge in two realistic scenarios

## Definition of System

System is physical server, virtual machine or a cloud platform. Protecting of such systems is crucial

#### Questions

1. Can cyber attacks happen without victim intervention (Yea/Nay)?
   **Answer:** Yes, because the system could be hacked without participation of third-parties
2. Can a breach of just a single system lead to disastrous consequences (Yea/Nay)?
   **Answer:** Yea

## Attacks on Systems

Independent from the attacker's motivation the first goal is to gain access to the target system. There are several types of attacks

#### Human-Led Attacks

Users are often those who start the attack: By inserting a malicious USB, downloading malware or using weak password

#### Vulnerabilities

Every piece of software can have security flaws. Moreover, IT administrators often increase the risks by setting weak passwords and allowing unrestricted access to their systems.

#### Supply Chain

Your PC is home to hundreds of apps, including web browsers, messengers, development, and entertainment software. Every app depends on thousands of libraries. If threat actors manage to breach one of the apps or libraries and push an update to all its users, all of them will be compromised

#### Questions

1. What is the term for a security flaw that can be exploited to breach a system?
   **Answer:** Vulnerability
2. What is the name of the attack when malware comes from a trusted app or library?
   **Answer:** Supply Chain

## Vulnerabilities

#### Software Vulnerabilities

Every software do have a security flaw, some needs more time to be discovered. Zero-day vulnerabilities is the vulnerability that wasn't known before anyone else. Once a vulnerability is made public, it is assigned a Common Vulnerabilities and Exposures (CVE) number. From that number it's a race - attack develop exploits when defenders are implementing security measures.

#### Responding to Vulnerabilities

An answer to a CVE is always **a patch** - an update supplied by the software vendor. Even for zero-days, you'll have to wait for a patch, vigilantly monitor for exploitation traces, and try to survive the stressful period before the patch is released. For example, by:

- Restricting access to the system to only trusted IPs
- Applying temporary measures provided by the vendor
- Blocking known attack patterns on IPS or WAF

#### Questions

1. What is the CVE for the critical SharePoint vulnerability dubbed "ToolShell"?
   **Answer:** CVE-2025-53770
2. How would you respond to a detected vulnerability on your system?
   **Answer:** patch

## Misconfigurations

On the other hand, a misconfiguration isn't a bug in the software but a mistake in how the system was set up, often by the IT team. These errors happen frequently, usually to make things simpler, like using "1111" instead of typing a long password every time. Let's take a look at some real-world examples.

- How ["123456" password](https://www.bleepingcomputer.com/news/security/123456-password-exposed-chats-for-64-million-mcdonalds-job-chatbot-applications/) exposed chats for 64 million McDonald's job applications
- How a [misconfigured AWS cloud](https://www.bleepingcomputer.com/news/security/capital-one-data-breach-affects-106-million-people-suspect-arrested/#:~:text=intrusion%20occurred%20through%20a%20misconfigured%20web%20application%20firewall) resulted in a breach of 106 million bank customers
- How improperly configured [smart fridges](https://www.sectigo.com/resource-library/when-refrigerators-attack-how-cyber-criminals-infect-appliances-and-how-manufacturers-can-stop-them) are silently used in full-scale botnet attacks

#### Responding to Misconfigurations

Misconfigurations do not require a software update - just a better setup. As a SOC analyst, you'll often spot them only after threat actors exploit them. However, in smaller companies, you might also be responsible for a more proactive response, for example:

- **Penetration Testing**: Hire ethical "hackers" who simulate an attack and report on discovered security flaws
- **Vulnerability Scans**: Periodically run tools that can detect default passwords or outdated software
- **Configuration Audits**: Manually review the systems to match best practices like CIS benchmarks

#### Questions

1. Can a system patch or software update fix the misconfigurations (Yea/Nay)?
   **Answer:** Nay
2. Which activity involves an authorized cyber attack to detect the misconfigurations?
   **Answer:** Penetration Testing

## Practice

#### Questions

1. What flag did you receive after completing the "Systems at Risk" challenge?
   **Answer:** THM{patch_or_reconfigure?}
2. What flag did you receive after completing the "Remediation Plan" challenge?
   **Answer:** THM{best_systems_defender!}
