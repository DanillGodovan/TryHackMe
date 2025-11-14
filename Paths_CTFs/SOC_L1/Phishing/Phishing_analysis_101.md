## Introduction

Spam and Phishing are common social engineering attacks. In social engineering, phishing attack vectors can be a phone call, a text message, or an email. As you should have already guessed, our focus is on email as the attack vector.

## The Email Address

The invention of the email dates back to the 1970s for [ARPANET](https://www.britannica.com/topic/ARPANET).
So, what makes up an email address?

1. **User Mailbox (or Username)**
2. **@**
3. **Domain**

#### Questions

1. Email dates back to what time frame?
   **Answer:** 1970s

## Email Delivery

There are 3 specific protocols designed to facilitate the outgoing and incoming email messages:

- SMTP (Simple Mail Transfer Protocol) - It is utilized to handle the sending of emails
- POP3 (Post Office Protocol) - Is responsible transferring between a client and mail server
- IMAP (Internet Message Access Protocol) - Transfer between client and mail server
  What are differences between POP3 and IMAP?
- POP3
  - Emails are downloaded and stored on a single device
  - Sent messages are stored on the single device from which the email was sent
  - Emails can only be accessed from the single device the emails were downloaded to
  - If you want to keep messages on the server you need to check "Keep email on server" option, otherwise it will be deleted when it was downloaded to the single device
- IMAP - Emails are stored on the server and can be downloaded to multiple devices. - Sent messages are stored on the server. - Messages can be synced and accessed across multiple devices.
  That's diagram how email travels from the sender to the recipient.![[Pasted image 20251102214155.png]]

1. Alexa composes an email to Billy (`billy@johndoe.com`) in her favorite email client. After she's done, she hits the send button.
2. The **SMTP** server needs to determine where to send Alexa's email. It queries **DNS** for information associated with `johndoe.com`.
3. The **DNS** server obtains the information `johndoe.com` and sends that information to the **SMTP** server.
4. The **SMTP** server sends Alexa's email across the Internet to Billy's mailbox at `johndoe.com`.
5. In this stage, Alexa's email passes through various **SMTP** servers and is finally relayed to the destination **SMTP** server.
6. Alexa's email finally reached the destination **SMTP** server.
7. Alexa's email is forwarded and is now sitting in the local **POP3/IMAP** server waiting for Billy.
8. Billy logs into his email client, which queries the local **POP3/IMAP** server for new emails in his mailbox.
9. Alexa's email is copied (**IMAP**) or downloaded (**POP3**) to Billy's email client.

#### Questions

1. What port is classified as Secure Transport (STARTTLS) for SMTP?
   **Answer:** 587
2. What port is classified as Secure Transport for IMAP?
   **Answer:** 993
3. What port is classified as Secure Transport for POP3?
   **Answer:** 995

## Email Headers

there are two parts to an email:

- the email **header** (information about the email, such as the email servers that relayed the email)
- the email **body** (text and/or HTML formatted text)

What do you look for when analyzing a potentially malicious email?
Let's start with the following email header fields:

1. **From** - the sender's email address
2. **Subject** - the email's subject line
3. **Date** - the date when the email was sent
4. **To** - the recipient's email address
   Another method to obtain the same email header information, and more, is by viewing the '**raw**' email details.

there are other email header fields of interest.

1. **X-Originating-IP** - The IP address of the email was sent from (this is known as an **[X-header](https://help.returnpath.com/hc/en-us/articles/220567127-What-are-X-headers-)**)
2. **Smtp.mailfrom**/**header.from** - The domain the email was sent from (these headers are within **Authentication-Results**)
3. **Reply-To** - This is the email address a reply email will be sent to instead of the **From** email address
   To clarify, in the email in the sample above, the **Sender** is newsletters@ant.anki-tech.com, but if a recipient replies to the email, the response will go to reply@ant.anki-tech.com, which is the **Reply-To**, and **NOT** to newsletters@ant.anki-tech.com.

#### Questions

1. What email header is the same as "Reply-to"?
   **Answer:** Return-Path
2. Once you find the email sender's IP address, where can you retrieve more information about the IP?
   **Answer:** http://www.arin.net

## Email Body

There are text-only, html formatted emails. HTML Formatted emails do have hyperlinks and embeds and you are able to see their html code in the source.
We can see the headers associated with this attachment:

- **Content-Type** is **application/pdf**.
- **Content-Disposition** specifies it's an **attachment**.
- **Content-Transfer-Encoding** tells us it's **base64** encoded. 
  With the base64 encoded data, you can decode it and save it to your machine.

#### Questions

1. In the above screenshots, what is the URI of the blocked image?
   **Answer:** https://i.imgur.com/LSWOtDI.png
2. In the screenshots above, what is the name of the PDF attachment?
   **Answer:** Payment-updateid.pdf
3. In the attached virtual machine, view the information in email2.txt and reconstruct the PDF using the base64 data. What is the text within the PDF?
   **Answer:** THM{BENIGN_PDF_ATTACHMENT}

## Types of phishing

There are different types of malicious emails:

- Span - Junk Emails sent out in bulk to a large number of recipients. Most malicious of it also named MalSpam
- Phishing - Emails sent to a target pretending to be from a trusted entity
- Spear phishing - specifies individuals or organization seeking sensitive information
- Whaling - Subtype of spear phishing which goal is to get CEO, CFO, etc
- Smishing - Phishing to mobile devices by targeting mobile users
- Vishing - is similar to smishing but based on voice calls

This are typical characteristics phishing emails have in common:

- Email spoofing or masquerading as a trusted entity
- The email subject line is written with a sense of urgency or uses certain keywords such as Invoice, Suspended, etc.
- The email body is designed to match a trusting entity
- Also it's poorly formatted or written
- Uses generic content such as Dear Sir/Madam
- Hyperlinks (hiding a real link using shortening services)
- malicious attachment

#### Questions

1. What trusted entity is this email masquerading as?
   **Answer:** Home Depot
2. What is the sender's email?
   **Answer:** support@teckbe.com
3. What is the subject line?
   **Answer:** Order Placed : Your Order ID OD2321657089291 Placed Successfully
4. What is the website for the - CLICK HERE URL in a defanged format? (e.g. https://website.thm)
   **Answer:** hxxp\[://]t\[.\]teckbe\[.\]com
