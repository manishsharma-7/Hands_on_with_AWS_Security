# Build a Security Monitoring System

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-monitoring)

**Author:** Manish Sharma  
**Email:** manishsharma7@hotmail.com

---

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-monitoring_reghtjy)

---

## Introducing Today's Project!

In this project, I will demonstrate how to set up, monitor and secure access to AWS secrets using CloudTrail, CloudWatch, and SNS.

I’m doing this project to learn how to track secret access events, set up logging and alerting, and compare different notification approaches to improve security monitoring.

Key goals include:

🏞️ Set up AWS CloudTrail to track access to secrets.
🔎 Use AWS CloudWatch to log access attempts and trigger notifications.
🔔 Create SNS alerts to get notified whenever secrets are accessed.
💎 Build a second notification system and compare which method provides more effective and timely security alerts.

### Tools and concepts

Services I used were:
-AWS Secrets Manager – to securely store and retrieve secrets.
-AWS CloudTrail – to record API activity and secret access events.
-AWS CloudWatch Logs and Metrics – to monitor access and create actionable alarms.
-Amazon SNS – to receive notifications when a secret is accessed.

Key concepts I learnt include:
-How to securely manage secrets without hardcoding credentials.
-How to track secret access using CloudTrail events.
-Setting up CloudWatch Metrics and Alarms for specific security events.
-Configuring SNS notifications to alert me when critical events occur.

### Project reflection

This project took me approximately 2-3 hours.

The most challenging part was troubleshooting the CloudWatch Alarm and SNS notifications to ensure I received alerts correctly.

It was most rewarding to successfully validate the monitoring system and receive an email notification when my secret was accessed, confirming that the setup works end-to-end.

---

## Create a Secret

Secrets Manager is an AWS service that securely stores, manages, and retrieves sensitive information such as API keys, passwords, and database credentials.

You could use Secrets Manager to:
-Store credentials for applications without hardcoding them in code.
-Grant controlled access to secrets for different users or services.
-Automatically rotate secrets on a schedule to improve security.
-Monitor and audit secret usage to detect unauthorized access.

This helps ensure that sensitive data is protected, auditable, and easily manageable.

To set up for my project, I created a secret in AWS Secrets Manager called TopSecretInfo.

In the Key/Value tab, I entered:

Key: The Secret
Value: A fun or random secret — for example, "I need 3 coffees a day to function" or "Rice is the best carb"

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-monitoring_o5p6q7r8)

---

## Set Up CloudTrail

CloudTrail is an AWS service that records all API calls and account activity in your AWS environment, providing a detailed audit log of actions taken by users, roles, or services. 

I set up a trail to monitor and log all access to my secrets, so I can track who accessed them, when, and from where. This helps improve security visibility, auditing, and incident response.

CloudTrail events include types like:
-Management events – These record operations that create, modify, or delete AWS resources, such as creating a secret in Secrets Manager or launching an EC2 instance.
-Data events – These record operations performed on the resource data itself, like reading an object from an S3 bucket or invoking a Lambda function.
-Insights events – These capture unusual API activity patterns, helping detect anomalous behavior in your account.

By monitoring these events, I can track, analyze, and respond to all significant actions in my AWS environment.

### Read vs Write Activity

Read API activity involves operations that view or retrieve information from AWS resources, such as listing secrets in Secrets Manager or reading objects from an S3 bucket.

Write API activity involves operations that create, modify, or delete resources or data, such as creating a new secret, updating a secret value, or deleting an object from S3.

For this project, we need both Read and Write activities logged so we can monitor access attempts and changes to our secrets, ensuring full visibility into how sensitive information is being used.

---

## Verifying CloudTrail

I retrieved the secret in two ways:
First, through the Secrets Manager console – I navigated to my TopSecretInfo secret, scrolled to the Overview section on the secret details page, and selected Retrieve secret value to view it.

Second, using the AWS CLI – I ran the command:

aws secretsmanager get-secret-value --secret-id "TopSecretInfo" --region your-region-code

This allowed me to programmatically access the secret and confirmed that both the console and CLI methods work correctly.

To analyze my CloudTrail events, I visited the CloudTrail console and filtered for events related to secretsmanager.amazonaws.com.

I found an event called GetSecretValue, which indicates that my secret’s value was retrieved or accessed.

This tells me that CloudTrail successfully recorded the secret access, giving me visibility into when and how sensitive information was used.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-monitoring_s8t9u0v1)

---

## CloudWatch Metrics

CloudWatch Logs is an AWS Monitoring service that collects and stores logs from AWS resources, applications, and services, allowing you to search, monitor, and analyze events in real time.

It’s important for monitoring because it lets me track activity, detect anomalies, and respond quickly to suspicious actions, such as unauthorized access to secrets.

CloudTrail’s Event History is useful for quickly viewing and searching recent API activity in your account, providing a convenient audit trail of actions taken for last 90 days.

CloudWatch Logs, on the other hand, are better for real-time monitoring, alerting, and analyzing trends over time with advance filtering, making them ideal for detecting unusual behavior or security events as they happen.

A CloudWatch metric is a measurable data point that tracks the frequency or status of specific events in your AWS environment, such as secret access attempts.

When setting up a metric, the metric value represents how many times the event occurs within a given period.

The default value is used when no events are recorded during a period, ensuring the metric still reflects that zero activity occurred rather than leaving a gap in monitoring.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-monitoring_a9b0c1d2)

---

## CloudWatch Alarm

CloudWatch alarm is a monitoring tool that automatically notifies you or takes action when a metric crosses a specified threshold.

I set my CloudWatch alarm threshold to 1 so the alarm will trigger whenever the SecretIsAccessed metric shows that my secret has been accessed at least once.

I created an SNS topic along the way. An SNS topic is a communication channel that allows messages to be published and delivered to multiple subscribers, such as email addresses, SMS, or other services.

My SNS topic is set up to send notifications to my email, so I receive real-time alerts whenever my CloudWatch alarm detects secret access events.

AWS requires email confirmation for SNS subscriptions because it ensures that the recipient actually owns the email address and consents to receive notifications.

This helps prevent unauthorized subscriptions, spam, or accidental notifications being sent to someone who did not request them, improving the security and reliability of the alerting system.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-monitoring_fsdghstt)

---

## Troubleshooting Notification Errors

To test my monitoring system, I retrieved my secret value again from the Secrets Manager console, hoping to trigger the CloudWatch Alarm.

The results were unexpected: I didn’t receive an email notification. This told me that some troubleshooting was needed to ensure my monitoring system would alert me correctly when a secret is accessed.

When troubleshooting the notification issues, I checked several parts of my monitoring setup to find where things might be breaking:

-CloudTrail – to verify it recorded the GetSecretValue event.
-CloudTrail → CloudWatch integration – to ensure logs are being sent correctly.
-CloudWatch metric filter – to confirm it’s filtering logs for the specific secret access events.
-CloudWatch Alarm – to see if it’s properly triggering an action when the metric threshold is reached.
-SNS delivery – to ensure emails are being sent to my subscribed address.

This systematic approach helps pinpoint where the notifications are failing.

I initially didn’t receive an email because my CloudWatch Alarm configuration was incorrect.

The key solution was to update the alarm’s settings in the CloudWatch console:

-I checked the Statistic field and changed it from Average to Sum, because Sum counts all occurrences of secret access during the period, whereas Average might never cross the threshold.
-I also adjusted the Period from 5 minutes to 1 minute, so the alarm would trigger faster.
-I verified the Threshold type was set to Static and the condition was Greater/Equal than 1.
-After reviewing these settings, I updated the alarm.

These changes ensured that my CloudWatch Alarm would accurately detect secret access and trigger SNS notifications.

---

## Success!

To validate that my monitoring system was working, I checked the CloudWatch Alarm after retrieving the secret value.

I received an email from AWS Notifications with the subject: ALARM: "SecretIsAccessedAlarm", confirming that my CloudWatch Alarm and SNS notifications were now functioning correctly.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-monitoring_ageraergearge)

---

## Comparing CloudWatch with CloudTrail Notifications

In a project extension, I updated my CloudTrail configurations because I wanted to integrate SNS notifications, allowing me to be immediately informed whenever CloudTrail delivers a new log file to my S3 bucket.

When you enable SNS notification delivery in CloudTrail, it sends a notification to your SNS topic every time a new log file is delivered. This is different from a CloudWatch Alarm, which only triggers when a specific pattern or metric, such as secret access, is detected in the logs.

After enabling CloudTrail SNS notifications, my inbox was flooded with emails!

This happens because CloudTrail batches events over a short period and creates a new log file for every batch, triggering an SNS notification for each file. Since CloudTrail captures all management events in my AWS account, not just secret access, I received multiple notifications for various activities.

In terms of usefulness, I realized that CloudWatch Alarms are more targeted and practical for monitoring specific events, like secret access. In production, CloudTrail SNS notifications are usually sent to security monitoring systems or audit tools for programmatic processing, while CloudWatch Alarms provide actionable alerts that humans can respond to directly.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-monitoring_d7e8f9g0)

---

---
