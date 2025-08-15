# Threat Detection with GuardDuty

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-guardduty)

**Author:** Manish Sharma  
**Email:** manishsharma7@hotmail.com

---

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-guardduty_sm42x3y4)

---

## Introducing Today's Project!

### Tools and concepts

The services I used were AWS CloudFormation, EC2, S3, GuardDuty, and CloudShell. Key concepts I learnt include deploying a web app, simulating attacks like SQL and command injection, stealing and using credentials, detecting suspicious activity with GuardDuty, and implementing malware protection for S3 objects.

### Project reflection

This project took me approximately 2 hours. The most challenging part was simulating attacks safely while understanding how GuardDuty detects and reports suspicious activity. It was most rewarding to see the security alerts trigger in real time, confirming that AWS tools can detect unauthorized access and malware effectively.

I chose to do this project today because I wanted to learn how attackers can exploit vulnerabilities in a web application and how AWS security services like GuardDuty and Malware Protection can detect and respond to these threats. This project met my goals because I successfully simulated attacks, accessed sensitive data, and observed how GuardDuty identified both credential misuse and malware, giving me hands-on experience in cloud security monitoring.

---

## Project Setup

To set up for this project, I deployed a CloudFormation template that launches a vulnerable web application along with all the necessary AWS resources. The three main components are:

- Web app infrastructure  – The web app runs on an EC2 instance, which hosts the application files, processes incoming requests, and sends responses.

- Networking resources – The template creates a new VPC and all the associated networking resources, including:

Subnets
Security group
Internet gateway
Route tables
VPC endpoints
Elastic load balancer
Auto scaling group
Launch templates
Prefix lists

These resources host the EC2 instance and follow best practices by keeping it separate from the default VPC.

- Content delivery – A CloudFront distribution is deployed to speed up access to the web app, giving it a public URL that anyone can visit.

Together, these components create a fully functional, publicly accessible web application.

The web app deployed is called OWASP Juice Shop. To practice my GuardDuty skills, I will simulate attacks on the app, monitor the alerts generated, and analyze the detected suspicious activities to understand how AWS identifies and reports security threats.

GuardDuty is AWS’s threat detection service that continuously monitors your AWS accounts and workloads for malicious or unauthorized activity. In this project, it will detect attacks on the vulnerable web application, analyze suspicious activity, and alert me to potential security threats.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-guardduty_n1o2p3q4)

---

## SQL Injection

The first attack I performed on the web app is SQL injection, which means inserting malicious SQL code into input fields to manipulate the database. SQL injection is a security risk because it can allow attackers to bypass authentication, access sensitive data, modify or delete data, and potentially take control of the database.

My SQL injection attack involved entering ' or 1=1;-- into the login form to bypass authentication. This means the application treated the input as a valid SQL command, allowing me to log in without knowing the actual credentials.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-guardduty_h1i2j3k4)

---

## Command Injection

Next, I used command injection, which is a technique where an attacker executes arbitrary commands on the host system through a vulnerable application. The Juice Shop web app is vulnerable to this because it does not properly validate or sanitize user input, allowing malicious commands to be executed on the server.

To run command injection, I entered a malicious command in the Set Username field. The script will execute the injected command on the server, which caused the profile's username to change to [object Object], showing that the input was interpreted as code rather than plain text.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-guardduty_t3u4v5w6)

---

## Attack Verification

To verify the attack's success, I accessed the credentials file at [JuiceShopURL]/assets/public/credentials.json.

The credentials page showed me a JSON file containing AWS access information:

-AccessKeyId and SecretAccessKey – the main keys for accessing AWS services.
-Token – adds extra security to the session and is used with the access keys.
-Expiration – indicates when the credentials will no longer be valid.
-Code and Type – show the status and type of the credentials.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-guardduty_x7y8z9a0)

---

## Using CloudShell for Advanced Attacks

The attack continues in CloudShell because using stolen credentials in a CloudShell session simulates an outsider accessing my AWS environment. Normally, CloudShell runs under my IAM user and is considered safe, but when I use the stolen credentials, GuardDuty detects that these credentials are being used from a different account/session, which is unusual and suspicious.

This helps me understand that it’s not CloudShell itself that triggers alerts, but the combination of stolen credentials and unusual actions, which is exactly how GuardDuty identifies potential security threats.

In CloudShell, I used "wget" to download the credentials file from the web app. Next, I ran a command using "cat" and "jq" to display and process the JSON data. 

cat shows the contents of the file.
jq formats the JSON data so it’s easier to read.

The response I’m seeing in the terminal is the contents of credentials.json, which are the AWS credentials stolen through the web app.

I then set up a profile, called stolen, to use the AWS credentials I downloaded from the web app and run commands as if I were an attacker. I had to create a new profile because CloudShell by default uses my own IAM user’s permissions, so creating the stolen profile lets me simulate using compromised credentials in a separate session.

An AWS CLI profile is a set of authentication settings, including access key, secret key, session token, and region, that lets you authenticate and access AWS services. You can have multiple profiles for different accounts or permission levels.

By creating the stolen profile, I can now perform actions using the stolen credentials and observe how GuardDuty detects unusual activity, while keeping my main user’s session separate.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-guardduty_j9k0l1m2)

---

## GuardDuty's Findings

After performing the attack, GuardDuty reported a high-severity finding within 3 mins. Findings are alerts that identify suspicious or malicious activity in your AWS environment. In this case, GuardDuty detected that credentials for the EC2 instance role GuardDuty-project-manish-TheRole-KEMa2olrQVd0 were used from a remote AWS account, indicating that the stolen credentials were used in an unusual location, signaling unauthorized access.

GuardDuty’s finding was called “UnauthorizedAccess:EC2/RemoteCredentialCompromise”, which means credentials created exclusively for the EC2 instance role GuardDuty-project-manish-TheRole-KEMa2olrQVd0 were used from a remote AWS account (585960000000), indicating potential unauthorized access. Anomaly detection was used because it helps GuardDuty identify when credentials are used in ways that deviate from normal behavior, such as from an unusual account or location. This was reported as a high-severity alert, first seen 8 minutes ago.

GuardDuty’s detailed finding reported that credentials for the EC2 instance role were used from a remote AWS account. The report included important details about the unauthorized access, including:

Resource affected – shows that the attack used an IAM role to access the Juice Shop’s S3 bucket.

Action – indicates that an object, specifically the secret information text file, was retrieved.

Location and IP address – provides the attacker’s location, which corresponds to the AWS Region used when running CloudShell with the stolen credentials.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-guardduty_v1w2x3y4)

---

## Extra: Malware Protection

For my project extension, I enabled Malware Protection for the S3 bucket. Malware is any software intentionally designed to harm, exploit, or gain unauthorized access to a computer system or data.

To test Malware Protection, I uploaded EICAR-test-file.txt to the S3 bucket. The uploaded file won’t actually cause damage because it’s a harmless test file, called the EICAR test file, developed by the European Institute for Computer Antivirus Research (EICAR) specifically to safely test security software.

Once I uploaded the file, GuardDuty instantly triggered a malware detection alert. This verified that Malware Protection is working correctly, as it detected a security risk in the S3 object EICAR-test-file.txt (EICAR-Test-File, not an actual virus).

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-guardduty_sm42x3y4)

---
