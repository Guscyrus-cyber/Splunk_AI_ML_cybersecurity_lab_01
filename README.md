**AI/ML Cybersecurity Lab 01: Authentication Attack Classification**

**Introduction**

This lab introduces the application of Artificial Intelligence (AI) and Machine Learning (ML) concepts to cybersecurity authentication monitoring and attack detection. The objective is to analyze authentication events and identify suspicious login activity using classification techniques commonly employed in modern Security Operations Centers (SOCs).

In this scenario, I investigate authentication logs containing information such as usernames, source IP addresses, failed login attempts, login times, and indicators of new login locations. The dataset includes both normal user activity and simulated attack activity, allowing analysts to explore how machine learning models classify events as either legitimate or malicious.

The lab demonstrates the fundamental principles of supervised learning, where historical authentication events are labeled as either "Normal" or "Attack." By examining patterns in the data, analysts can determine which features contribute most to attack detection and understand how machine learning assists cybersecurity teams in identifying brute-force attacks, password spraying attempts, account compromise, and other authentication-related threats.

Using Splunk Enterprise, participants ingest the dataset, perform searches, analyze suspicious activity, and investigate authentication events in a manner similar to real-world SOC operations. Throughout the lab, I learn how AI/ML concepts such as features, labels, classification, anomalies, true positives, false positives, false negatives, and true negatives relate to cybersecurity monitoring and incident detection.

This project serves as the first hands-on AI/ML cybersecurity lab in the series and provides a foundation for future topics including anomaly detection, User and Entity Behavior Analytics (UEBA), phishing classification, malware classification, insider threat detection, and advanced threat hunting.\
\
**Dataset**

Name: auth_ml_dataset.csv

Please refer to screenshot images # 1 and 2 


**Uploading to Splunk**

Please refer to screenshot image # 3


Search: source="auth_ml_dataset.csv" host="MacBookPro" index="auth" sourcetype="auth_ml"

Splunk successfully indexed 50 authentication events from the auth_ml_dataset.csv dataset through using the query “ source="auth_ml_dataset.csv" host="MacBookPro" index="auth" sourcetype="auth_ml". The screenshot displays the first five events as a sample to verify successful ingestion and field extraction before beginning the classification analysis.

Please refer to screenshot # 4

. Which account accumulated the highest number of failed logins across all events?

using search:\
index=auth sourcetype=auth_ml
/| sort - failed_logins
/| head 5
/| table user failed_logins label

Please refer to screenshot # 5

| **User**  | **Failed Logins** | **Label** |
|-----------|-------------------|-----------|
| guest     | 160               | Attack    |
| root      | 140               | Attack    |
| admin     | 120               | Attack    |
| svc_admin | 110               | Attack    |
| admin     | 95                | Attack    |

The accounts with the highest failed login counts were:

guest (160 failed logins)\
root (140 failed logins)\
admin (120 failed logins)\
svc_admin (110 failed logins)\
admin (95 failed logins)\
So the admin accumulated the highest number of failed logins across all events.\
\
. Which authentication event had the highest number of failed logins?\
guest\
\
. Which accounts are labeled Attack?\
I identified multiple privileged and administrative accounts with unusually high failed login activity. All five events were labeled as **Attack**, indicating a strong likelihood of brute-force or password spraying activity. The accounts **guest**, **root**, **admin**, and **svc_admin** should be prioritized for investigation because attackers commonly target administrative accounts to gain unauthorized access to systems and sensitive resources.

. Which users logged in from new IPs?\
\
Search:\
index=auth sourcetype=auth_ml

Please refer to screenshot images # 6 and 7

new_ip="Yes"

\| table user src_ip failed_logins successful_login_after_failures label

I filtered authentication events where the new_ip field was set to **Yes**. Authentication activity originating from a new IP address may indicate legitimate user behavior, such as connecting from a different network, but it can also be a sign of account compromise or unauthorized access attempts. To determine the level of risk, I reviewed the failed login counts, successful logins after failures, and classification labels associated with each event.

The following users authenticated from new IP addresses:

Mike\
mary\
sarah\
alice\
David\
admin\
svc_admin\
guest\
root

A total of **19 events** were identified with new_ip="Yes".

Both normal and attack events were associated with new IP addresses. However, the most suspicious activity involved the accounts:

Admin\
root\
guest\
svc_admin

These accounts generated high failed login counts ranging from **55 to 160 failed logins** and were classified as **Attack** events.

I determined that a new IP address alone is not sufficient to classify an event as malicious because several normal user accounts also logged in from new IP addresses. However, when a new IP address was combined with high failed login counts and successful logins after repeated failures, the likelihood of malicious activity increased significantly. The accounts **admin**, **root**, **guest**, and **svc_admin** exhibited the strongest indicators of potential brute-force attacks, password spraying activity, or account compromise and should be prioritized for investigation.

. Which accounts may indicate a successful compromise after repeated failed login attempts?\
\
using this query:\
index=auth sourcetype=auth_ml
successful_login_after_failures="Yes"
\| table user src_ip failed_logins label\

Please refer to image # 8

I identified seven authentication events where the dataset indicates that a successful authentication occurred after repeated failed login attempts. All matching events were labeled as **Attack** and involved high failed login counts ranging from 55 to 140 attempts. These events may represent successful brute-force attacks or account compromise attempts and should be prioritized for investigation.
