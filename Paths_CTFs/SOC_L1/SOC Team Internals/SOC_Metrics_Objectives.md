#### Learning Objectives

- Discover the concepts of SLA, MTTD, MTTA, and MTTR
- Understand the importance of the False Positive rate
- Learn why and how to improve the metrics as an L1 analyst
- Practice with managing SOC team performance metrics

#### Prerequisites

[[SOC Workbooks and Lookups]]
[[SOC L1 Alert Reporting]]
[[SOC L1 Alert Triage]]

## Core Metrics

The core goal of SOC is to follow CIA Triad. SOC Team performs that purpose by developing, receiving and triaging alerts. L1 SOC need to reliably report TP (True Positives) to L2. There are four metrics for that:

| Metric                | Formula                                  | Measures                     |
| --------------------- | ---------------------------------------- | ---------------------------- |
| Alerts Count          | `AC = Total Count of Alerts Received`    | Overall load of SOC analysts |
| False Positive Rate   | `FPR = False Positives / Total Alerts`   | Level of noise in the alerts |
| Alert Escalation Rate | `AER = Escalated Alerts / Total Alerts`  | Experience of L1 analysts    |
| Threat Detection Rate | `TDR = Detected Threats / Total Threats` | Reliability of the SOC team  |

#### Alerts Count

The ideal metric value depends on company size but in general, **5 to 30 alerts per day per L1 analyst is a good metric**.

#### False Positive Rate

If 75 out of 80 alerts (94%) were confirmed to be False Positives - that's bad signal for your team. If your team treats all false positives instead of working on TP - it's a bad sign. 80% or higher is a serious problem, which could be solved by tool and detection rules tuning (also False Positive Remediation)

#### Alert Escalation Rate

The alert escalation rate comes in handy to evaluate how experienced and independent the L1 analysts are and how often they decide to escalate the alert. It is usually **aimed to be below 50%, or even better below 20%**.

#### Threat Detection Rate

The threat detection rate **should always be at 100%** since every missed threat can have devastating consequences, such as ransomware infection and data exfiltration.

#### Questions

1. Is zero alerts for one month a good sign for your SOC team? (Yea/Nay)
   **Answer:** Nay
2. What is the False Positive Rate if only 10 out of 50 alerts appear to be real threats?
   **Answer:** 80%

## Triage Metrics

The requirements to ensure a quick detection and remediation of the threat are commonly grouped into a Service Level Agreement (SLA) - document that signed Company Management and the SOC Team, or by managed SOC Provider (MSSP) and its customers. The agreement usually requires quick threat detection (measured with MTTD, which stands for Mean Time To Detect), timely alert acknowledgement by L1 Analysts (measured by MTTA - Mean Time To Acknowledge), and finally, prompt response to the threat, like isolating the device or securing the breached account (measured by MTTR - Mean Time To Response)![[Pasted image 20251109225207.png]]

#### Reference Table

Note that different teams might have different definitions or formulas for the SOC metrics, depending on what they want to measure.

| Metric                          | Common SLA | Description                                                              |
| ------------------------------- | ---------- | ------------------------------------------------------------------------ |
| SOC Team Availability           | 24/7       | Working schedule of the SOC team, often Monday-Friday (8/5) or 24/7 mode |
| Mean Time to Detect (MTTD)      | 5 minutes  | Average time between the attack and its detection by SOC tools           |
| Mean Time to Acknowledge (MTTA) | 10 minutes | Average time for L1 analysts to start triage of the new alert            |
| Mean Time to Respond (MTTR)     | 60 minutes | Average time taken by SOC to actually stop the breach from spreading     |

#### Questions

1. Imagine a scenario where the SOC team receives a critical alert on Saturday.  
   If the team works 8/5, on which day of the week will they acknowledge the alert?
   **Answer:** Monday
2. Imagine a scenario where an employee was lured into running data stealer malware.  
   The SOC team received the "Connection to Redline Stealer C2" alert after **12** minutes.
   One of the L1 analysts on shift moved the alert to In Progress **10** minutes later.
   After **6** minutes, the alert was escalated to L2, who spent **35** minutes cleaning the malware.  
   Provide the MTTD, MTTA, and MTTR via comma as your answer (e.g. 10,20,30).
   **Answer:** 12, 10, 51

## Improving Metrics

#### Reference Table

| Issue                                    | Recommendations                                                                                                                                                                                                                                           |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| False Positive Rate <br>over 80%         | **Your team receives too much noise in the alerts. Try to:** <br> <br>1. Exclude trusted activities like system updates from your EDR or SIEM detection rules <br>2. Consider automating alert triage for most common alerts using SOAR or custom scripts |
| Mean Time to Detect <br>over 30 min      | **Your team detects a threat with a high delay. Try to:** <br> <br>1. Contact SOC engineers to make the detection rules run faster or with a higher rate <br>2. Check if SIEM logs are collected in real-time, without a 10-minute delay                  |
| Mean Time to Acknowledge <br>over 30 min | **L1 analysts start alert triage with a high delay. Try to:** <br> <br>1. Ensure the analysts are notified in real-time when a new alert appears <br>2. Try to evenly distribute alerts in the queue between the analysts on shift                        |
| Mean Time to Respond <br>over 4 hours    | **SOC team can't stop the breach in time. Try to:** <br> <br>1. As L1, make everything possible to quickly escalate the threats to L2 <br>2. Ensure your team has documented what to do during different attack scenarios                                 |

#### Questions

1. What is the highest acceptable False Positive Rate for SOC teams?
   **Answer:** 80%
2. Should all SOC roles work together to keep metrics improving? (Yea/Nay)
   **Answer:** Yea

## Practice Scenarios

1. What flag did you get after completing the first scenario?
   **Answer:** THM{mttr:quick_start_but_slow_response}
2. What flag did you get after completing the second scenario?
   **Answer:** THM{mttd:time_between_attack_and_alert}
3. What flag did you get after completing the third scenario?
   **Answer:** THM{fpr:the_main_cause_of_l1_burnout}
