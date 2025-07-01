<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Security Monitoring System

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-monitoring)

**Author:** David Ivory  
**Email:** davidmivory@gmail.com

---

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-monitoring_reghtjy)

---

## Introducing Today's Project!

In this project, I will demonstrate how to set up a monitoring system using AWS CloudTrail, CloudWatch, and SNS to track when sensitive secrets are accessed. I’m doing this project to sharpen and showcase my ability to detect and respond to security threats in real time. By logging access attempts and configuring alert workflows, I’m applying practical experience with tools used by Security Engineers and DevOps teams to protect critical data. I’ll also build and evaluate a second alerting system to compare effectiveness and show how each service supports a real incident response strategy. By the end, I’ll have a working solution that reflects best practices in cloud monitoring and highlights my ability to design secure and well-instrumented environments.

### Tools and concepts

Services I used were AWS Secrets Manager, CloudTrail, CloudWatch Logs, CloudWatch Alarms, and SNS. Key concepts I learnt include how to track and respond to secret access events, the difference between management and data events in CloudTrail, how to convert logs into actionable metrics in CloudWatch, and how to set up real-time alerts using SNS. This project pulled together logging, monitoring, and alerting into a complete security response workflow.

### Project reflection

This project took me approximately an hour and a half to complete end to end. The most challenging part was fine-tuning the CloudWatch alarm settings to trigger alerts reliably without too much noise. It was most rewarding to see real-time alerts hit my inbox after accessing the secret, knowing the monitoring system was working exactly as intended.

---

## Create a Secret

Secrets Manager is a managed service in AWS that lets you securely store, manage, and access sensitive information like API keys, database credentials, and other secrets. It handles encryption automatically and helps rotate credentials to reduce security risks. Instead of hardcoding secrets into your applications, you can call them securely from Secrets Manager when needed.

To set up for my project, I created a secret called TopSecretInfo that contains a retrievable secret value. 

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-monitoring_o5p6q7r8)

---

## Set Up CloudTrail

CloudTrail is an AWS service that records and logs API calls and account activity across your AWS environment. I set up a trail to continuously capture these events and store them securely in an S3 bucket. This trail acts like a detailed audit log, giving me visibility into actions such as accessing secrets, creating resources, or changing configurations. Having this record helps with security monitoring, compliance, and troubleshooting by showing exactly what happened, when, and by whom.

CloudTrail events include types like management events, data events, and insights events. Management events capture operations that manage AWS resources, such as creating or deleting a secret. Data events record activity on specific resources, like reading or writing objects in S3 or accessing Secrets Manager secrets. Insights events detect unusual or unexpected API activity to help identify potential security issues. Together, these event types give a comprehensive view of what’s happening in your AWS account.

### Read vs Write Activity

Read API activity involves actions that retrieve or view data without changing anything, like getting the value of a secret. Write API activity involves making changes to resources, such as creating, updating, or deleting a secret. For this project, we need to focus mainly on read activities since we want to monitor who is accessing sensitive information. Tracking read events helps catch when someone views the secret, which is critical for spotting potential security risks.

---

## Verifying CloudTrail

I retrieved the secret in two ways: First through the AWS Management Console by manually viewing the secret’s value. Second using the AWS CLI by running a command: 
'$ aws secretsmanager get-secret-value --secret-id "TopSecretInfo" --region us-east-2' to fetch the secret programmatically. Both methods generate access events that CloudTrail can capture for monitoring.

To analyze my CloudTrail Events, I visited Event History, filtered by Event Source, with the attribute being 'secretsmanager.amazonaws.com'. I found two events named "GetSecretValue', as well as other events showing the complete process of creating secrets then retreiving them. 

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-monitoring_s8t9u0v1)

---

## CloudWatch Metrics

CloudWatch Logs is a service that collects and stores log data from AWS resources and applications. It’s important for monitoring because it lets you search, analyze, and set alarms on specific events in real time. By feeding CloudTrail logs into CloudWatch Logs, I can create metrics and alerts that trigger instantly when something important, like secret access, happens, helping me respond quickly to potential security issues.











CloudTrail’s Event History is useful for auditing and reviewing past API activity across your AWS account, providing a detailed record of what happened and when. While CloudWatch Logs are better for real-time monitoring and analysis, letting you create metrics, set alarms, and trigger notifications based on specific events as they happen. Together, they provide both a historical view and immediate visibility into your environment.

A CloudWatch metric is a way to quantify specific events or measurements from your logs or resources. When setting up a metric, the metric value represents the numerical value assigned to each matching event, often a count of occurrences. Default value is used when no matching event is found during a period, letting you define what the metric reports when there’s no activity, which helps keep your monitoring accurate and consistent.

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-monitoring_a9b0c1d2)

---

## CloudWatch Alarm

A CloudWatch alarm is a tool that monitors a specific metric and triggers actions when that metric crosses a defined threshold. I set my CloudWatch alarm threshold to a static value of 1, so the alarm will trigger whenever the SecretIsAccessed metric is greater than or equal to 1. This means the alarm goes off as soon as the secret is accessed even once, ensuring I get notified immediately of any access.

I created an SNS topic along the way. An SNS topic is a communication channel that allows messages to be sent to multiple subscribers at once, such as email addresses or other endpoints. My SNS topic is set up to send email notifications whenever the CloudWatch alarm triggers, so I get alerted immediately if the secret is accessed.

AWS requires email confirmation because it ensures that the person receiving notifications actually wants to subscribe to the topic. This helps prevent unwanted or spammy messages from being sent to people who did not opt in, protecting users from unsolicited alerts and maintaining security and privacy.

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-monitoring_fsdghstt)

---

## Troubleshooting Notification Errors

After retrieving the secret value again, there was no email alert as expected. 

When troubleshooting the notification issues I first retrieved the secret, utilized the CLI to manually trigger the alarm: 
$ aws cloudwatch set-alarm-state --alarm-name "Secret is accessed" --state-value ALARM --state-reason "Manually triggered for testing"                                                                                                                                            
~ $ aws cloudwatch set-alarm-state \
> --alarm-name "Secret is accessed" \
> --state-value ALARM \
> --state-reason "Manually triggered for testing"
I then went into CloudWatch alarms, and set the Statistic to Sum. 

I initially didn’t receive an email before because the alarm was set to use the average statistic, which meant occasional secret access events didn’t raise the average enough to cross the threshold. The key solution was switching the statistic to sum, so the alarm counts the total number of accesses during the period, ensuring it triggers as soon as there is at least one access.

---

## Success!

To validate, I repeatedly retrieved my secret value, checked my email, and ensure I recieved an email after each occurrence. 

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-monitoring_ageraergearge)

---

## Comparing CloudWatch with CloudTrail Notifications

In a project extension, I updated my CloudTrail configurations because I wanted to capture additional events and improve the detail of monitoring for secret access. This helped ensure that all relevant activity is logged and available for analysis, making the alerting system more accurate and effective at detecting suspicious behavior. As soon as I selected "Retrieve secret", I received an email. 

After enabling CloudTrail SNS notifications, my inbox started receiving immediate alerts whenever the secret was accessed. In terms of the usefulness of these emails, I thought they provided quick visibility but were sometimes too detailed and noisy, making it harder to spot critical events at a glance compared to using CloudWatch alarms for more targeted notifications.

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-monitoring_d7e8f9g0)

---

---
