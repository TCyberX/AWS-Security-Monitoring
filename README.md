
# Build a Security Monitoring System

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-monitoring)

**Author:** Albert  
**Email:** tapcyberx@gmail.com

---

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-monitoring_reghtjy)

---

## Introducing Today's Project!


In this project, I'm setting up a security monitoring system on AWS using:

AWS CloudTrail – Log and monitor account activity.

Amazon CloudWatch – Analyze logs and create alarms for specific events.

Amazon SNS (Simple Notification Service) – send real-time email alerts based on those alarms.

The goal is to understand how AWS security and monitoring services work together and to build a functional system that sends actionable email notifications. This setup will help me filter and respond to critical events like unauthorized access attempts or changes to security configurations.

### Tools and concepts

Services I worked with the following AWS services:

_ CloudTrail / Log and monitor account activity
_ CloudWatch / Log analysis, metric creation, and 
   alarms.
_ SNS (Simple Notification Service) Send real-time 
   alerts.
_ Secrets Manager / Securely store and access 
   sensitive data.
_  IAM / Manage roles and permissions.
_  S3 / Store log files.

Along the way, I' learned in this project:

-How to securely store and manage secrets.
-The difference between CloudTrail and CloudWatch.
-How notifications work and the types of endpoints you 
  can use (email, apps, etc.).
-How to create CloudWatch metric filters and set up 
  alarms for specific events.

This hands-on experience helped me understand how AWS monitoring and alerting services work together to enhance security and visibility.

### Project reflection

-This project took me about two days, largely due to time spent on troubleshooting. The most challenging part was like in many projects dealing with unexpected errors. But those errors became valuable learning moments.

-They pushed me to investigate issues from multiple angles, deepen my understanding of AWS services, and build stronger problem solving skills.

-The most rewarding part? Watching everything come together seeing the CloudWatch alarm trigger and successfully communicate with other services like SNS and Secrets Manager. It was a great hands-on reminder that troubleshooting is where real growth happens.

---

## Create a Secret

AWS Secrets Manager is a secure and scalable service designed to manage sensitive information such as database credentials, API keys, and other secrets. By storing secrets centrally, it eliminates the need to hardcode them in your application code, reducing the risk of accidental exposure.

Some of the key features:

Secure Storage and Encryption: Secrets are encrypted at rest using AWS Key Management Service (KMS), ensuring that your sensitive data remains protected. ​

Automatic Rotation: Secrets Manager can automatically rotate credentials for supported AWS services, like Amazon RDS, without disrupting applications. This helps maintain security best practices by regularly updating secrets. ​

Fine-Grained Access Control: Integrates with AWS Identity and Access Management (IAM) to control access to secrets, allowing you to define who or what can access specific secrets. ​


To set up for my project, I created a secret called TopSecretInfo at contains in secret description the string Secret created for NextWork's project on Building a Monitoring System.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-monitoring_o5p6q7r8)

---

## Set Up CloudTrail

AWS CloudTrail is a monitoring service that tracks API calls and account activity across your AWS environment.
It records details about who did what, when, and from where—making it invaluable for security (detecting suspicious activity), compliance (demonstrating adherence to policies), and troubleshooting (identifying what changed or went wrong).

A trail in CloudTrail is a configuration that enables these logs to be delivered to a storage location, such as an Amazon S3 bucket, and optionally to Amazon CloudWatch Logs for real-time monitoring and alerting. With a trail, you can ensure that every action taken in your AWS account is recorded and accessible for review, which is critical for maintaining a secure and compliant environment.

CloudTrail tracks several types of events: 

Management Events: Log control operations (e.g., resource access, modifications). I used these since accessing a secret is a management action and they're free to track.

Data Events: Capture high-volume data operations (e.g., S3 object access) and may incur extra costs.

Insights Events: Detect anomalies by analyzing API call patterns.

For this project, I set CloudTrail to track Management events, since accessing a secret falls under that category and it’s free to monitor in AWS.




### Read vs Write Activity

Read activity involves actions like accessing, viewing, or opening a resource.

Write activity includes creating, updating, or deleting a resource.

For this project, I selected both Read and Write activities in CloudTrail. However, only Write is essential, since accessing a secret is considered a Write action due to its security impact.

---

## Verifying CloudTrail

I retrieved the secret in two ways:

-- Through the Secrets Manager Console by clicking the “Retrieve secret value” button.
-- Using the AWS CLI in CloudShell, by running the get-secret-value command to securely access in the CLI.



To analyze my CloudTrail events, I reviewed the Event History and found a (GetSecretValue) event each time I "Retrieved the Secret", whether through the AWS Console or via CLI. 
This confirms that CloudTrail effectively tracks access to secrets, giving us full visibility into when and how a secret is accessed.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-monitoring_s8t9u0v1)

---

## CloudWatch Metrics

CloudWatch Logs is a monitoring service that collects log data from other AWS services, like CloudTrail, to help you analyze events and set up alarms. 

It’s a key tool for monitoring because it allows you to gain insights and get real-time alerts based on activities happening in your AWS account.

CloudTrail Event History is great for quickly viewing management events from the last 90 days.
In contrast, CloudWatch Logs is more powerful for long-term log storage, combining logs from multiple sources, and performing advanced filtering and analysis across your AWS environment.

A CloudWatch metric is a way to track or count specific events within a log group. When a metric filter is set up, the metric value increases (e.g., by 1) each time a log entry matches the defined pattern such as when a secret is accessed.

A default value is used when the tracked event doesn't occur during a given time period, ensuring consistent monitoring and alerting.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-monitoring_a9b0c1d2)

---

## CloudWatch Alarm

A CloudWatch alarm is an alerting feature that monitors metrics and triggers an alert when specific conditions are met. 
In my setup, I configured the alarm to watch how often the GetSecretValue event occurs. 

If this event happens more than once within a 5-minute period, the alarm will trigger helping detect unusual or excessive access to secrets.

An SNS topic is like a newsletter/broadcast channel that multiple endpoints like email addresses, phone numbers, or applications can subscribe to. 
When a message is published to the topic, all subscribers are notified instantly.

In this setup, I created an SNS topic that sends an email alert whenever our secret is accessed, helping me stay informed in real time.

AWS requires email confirmation to ensure that recipients consent to receive notifications. 
It prevents unauthorized or unwanted subscriptions, protecting users from spam or unexpected alerts. 

This extra step ensures that only those who explicitly confirm are added to the SNS topic.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-monitoring_fsdghstt)

---

## Troubleshooting Notification Errors

To test the setup, I accessed the secret again expecting an email notification. However, no email or alert was received, indicating that something in the monitoring or alerting workflow didn’t trigger as expected. 

This signals the need for further troubleshooting to identify and fix the issue.

To troubleshoot the missing notifications, I carefully investigated each part of the monitoring system, step by step:

-Checked if CloudTrail was logging the secret access event.
-Verified whether CloudTrail was sending logs to the correct CloudWatch Logs group.
-Reviewed the metric filter to ensure it wasn't accidentally ignoring valid events.
-Confirmed whether the CloudWatch alarm was triggering properly.
-Ensured the SNS topic was correctly configured to send email notifications when the alarm fired.

By breaking it down those steps I was able to isolate where the failure occurred and move closer to a working solution with the hepful guide of this project.

I initially didn’t receive an email because the CloudWatch alarm was using the wrong threshold type. It was set to calculate the (AVERAGE) number of times the secret was accessed during the time period, when it should have been using the (SUM).

Since access events are rare and not frequent, using (AVERAGE) caused the alarm not to trigger, even when the secret was accessed. After correcting this to use (SUM), the alert system started working as expected.

---

## Success!

To confirm the monitoring setup was working, I accessed the secret value one more time. Within 2–3 minutes, I received the email alert, and when I checked CloudWatch, the alarm was in the ALARM state. 

This confirmed that the system successfully detected the event and triggered the proper notification.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-monitoring_ageraergearge)

---

## Comparing CloudWatch with CloudTrail Notifications

In this extension of this project, I enabled SNS notification delivery directly from CloudTrail, instead of using CloudWatch. 
This allows real-time notifications for specific events (like when a secret is accessed) by sending alerts immediately through SNS without needing to set up a separate CloudWatch alarm, but in my personal opinion I like the functionalities of CloudWatch . 

After enabling SNS notifications directly from CloudTrail, my inbox was quickly flooded with emails. 

While it confirmed that new logs were being delivered to the S3 bucket, the notifications were overwhelming and lacked context ,they didn’t show which specific management events occurred, just that new logs were stored.

This experience of using CloudWatch offers more targeted, meaningful alerts, especially when you're looking to monitor specific actions like secret access or configuration changes.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-monitoring_d7e8f9g0)

---

---
