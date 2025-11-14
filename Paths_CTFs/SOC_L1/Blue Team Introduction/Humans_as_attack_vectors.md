## The Human Element

#### Why Humans Are Targeted

Humans are targeted because of the **access** they can provide - to websites, mailboxes, or databases. Some threat actors hunt for a specific access, while others are not so selective and just breach as many accounts as they can and decide how to use them later. Here are just a few examples of what threat actors hope for when trying to breach humans:

| Attack Example                                 | Next Step of Attackers                           |
| ---------------------------------------------- | ------------------------------------------------ |
| Breach Google account of the HR manager        | Steal and sell the entire employee database      |
| Trick a wealthy person into running malware    | Hijack a web banking session from their PC       |
| Breach the IT administrator's VPN account      | Access the heart of a big corporate network      |
| Trick a government worker into sharing secrets | Use the information to simplify the next attacks |

#### Questions

1. What or who is the weakest link in cyber security?
   **Answer:** Humans
2. What do attackers seek when targeting humans in a cyberattack?
   **Answer:** Access

## Attacks on Humans

For the tactic to succeed, it is designed to be:

- **Trustworthy:** The attacker must appear legitimate so the victim trusts them
- **Emotional:** The attack must trigger feelings like urgency, fear, or curiosity

#### Phishing Attacks

You receive an email saying, "_Your account has been compromised. Click here for details!_" When you click on the link, it takes you to a fake login page that looks real, but sends your credentials to the attacker. That's phishing in action. Email phishing is the most common form of social engineering, with an estimated [3.4 billion](https://aag-it.com/the-latest-phishing-statistics/#:~:text=with%20an%20estimated%203.4%20billion%20spam%20emails%20sent%20every%20day) malicious emails sent daily.

#### Malware Downloads

Ever searched for and installed the application on your PC? Well, you might have just installed [malware](https://www.esentire.com/blog/fake-deepseek-site-infects-mac-users-with-atomic-stealer#:~:text=The%20infection%20process%20begins%20when%20the%20user%20is%20redirected) on your computer. To make these attacks more successful, threat actors use innovative techniques like fake CAPTCHAs, malicious QR codes, and SEO poisoning.

#### Deepfakes

The rapid rise of AI-generated video or audio has become more effective in impersonating family members, colleagues, or corporate partners. [In one case](https://edition.cnn.com/2024/02/04/asia/deepfake-cfo-scam-hong-kong-intl-hnk), a finance worker received a deepfake video call from someone appearing to be their boss and got tricked into wiring $25 million for an "urgent business deal".

#### Impersonation

Even without deepfakes, the attackers are quite successful in pretending to be someone else (impersonating someone). Many [recent ransomware attacks](https://thehackernews.com/2024/12/black-basta-ransomware-evolves-with.html#:~:text=make%20initial%20contact%20with%20prospective%20targets) started with a simple phone call from attackers impersonating a corporate IT department and asking victims to take over their accounts for a quick "system repair".

#### Other Attacks

This task covered some popular social engineering methods, but there are many more! USB drop campaigns, physical attacks, insider threats, and even fake job offers - all of them are a constant risk to the employees, and you as a SOC analyst should be ready to respond!

#### Questions

1. What is the name of an attack tactic that manipulates human psychology?
   **Answer:** Social Engineering
2. Which social engineering method is about pretending to be someone else?
   **Answer:** Impersonation

## Defending Humans

Here are a few examples of how to protect employees:

| Mitigation                       | Description                                                                                           |
| -------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **Anti-phishing solution**       | SOC routine becomes easier with a tool that blocks phishing emails before the users even notice       |
| **Antivirus / EDR solution**     | A reliable antivirus on all corporate hosts is a great measure to prevent humans from running malware |
| **"Trust but verify" principle** | Instruct your employees how to detect deepfakes and verify suspicious requests from "CEO" or "IT"     |
| **Security awareness training**  | Teach your employees how to detect phishing and reinforce training through phishing simulations       |

#### Questions

1. Which process is aimed at preventing or reducing the chance of an attack?
   **Answer:** Mitigation
2. Which mitigation measure is about training employees in cyber security?
   **Answer:** Security Awareness Training

## Practice

#### Questions

1. What flag did you receive after completing the "Employees at Risk" challenge?
   **Answer:** THM{anyone_else_at_risk?}
2. What flag did you receive after completing the "Security Policy" challenge?
   **Answer:** THM{human_protection_expert!}
