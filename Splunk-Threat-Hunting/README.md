# Splunk Threat Hunting Lab

Overview

This lab demonstrates the use of Splunk for **threat hunting** by analyzing Windows Event Logs and Sysmon data to detect:
- Failed logon attempts
- Brute force attacks
- Suspicious PowerShell execution


Project Objectives

- Ingest Windows Security Event Logs and Sysmon logs into Splunk.
- Write SPL queries to detect security events.
- Visualize and interpret log data to identify malicious behavior.



Tools Used

- **Splunk Enterprise** (SIEM)
- **Windows Event Log**
- **Sysmon (System Monitor)**
- **PowerShell** (for simulation commands)



Steps

Step 1 – Verify Event Log Ingestion
Query to confirm data is being received:

index=main sourcetype=XmlWinEventLog
Result: Event logs successfully ingested.
![Step 1](Images/step1.png)

Step 2 – Detect Failed Logon Attempts

index=main sourcetype="XmlWinEventLog:Security" EventCode=4625
| table _time TargetUserName IpAddress WorkstationName FailureReason
| sort - _time

Result: Multiple failed logon attempts detected for user testuser.
![Step 2](Images/step2.png)

Step 3 – Identify Brute Force Attempts

index=main sourcetype="XmlWinEventLog:Security" EventCode=4625
| bin _time span=5m
| eval user=coalesce(TargetUserName, Account_Name, user)
| stats count values(FailureReason) AS reasons by _time user IpAddress WorkstationName
| where count >= 5
| sort - _time

Result: testuser had 6 failed login attempts in under 5 minutes — possible brute force activity.
![Step 3](Images/step3.png)


Step 4 – Detect Suspicious PowerShell Execution

index=main source="WinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1
| where like(Image, "%\\powershell.exe")
| search Image="C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe" 
         ParentImage="C:\\Windows\\explorer.exe"

Result: PowerShell execution detected originating from explorer.exe — unusual for normal operations.
![Step 4](Images/step4.png)


Summary of findings
  Effective log ingestion and parsing are critical for accurate detection
  Splunk queries can be tweaked to focus on niche and very specific attack patterns
  Visualizing the data helps identify any trends or anomalies quickly
