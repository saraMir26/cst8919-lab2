CST8919 Lab 2 – Building a Web App with Threat Detection using Azure Monitor and KQL
Student Information

Name: Sara Mirzaei
Course: CST8919 – DevOps Security and Compliance
Lab: Lab 2 – Building a Web App with Threat Detection using Azure Monitor and KQL

Overview

This lab demonstrates how Azure Monitor and Kusto Query Language (KQL) can be used to detect suspicious activity in a web application. A Python Flask application was deployed to Azure App Service and configured to log login attempts. Azure Monitor and Log Analytics were then used to analyze these logs and identify potential brute-force login attacks. Finally, an Azure Monitor alert was configured to automatically notify administrators when suspicious activity is detected.

Technologies Used
Python Flask
Azure App Service
Azure Monitor
Azure Log Analytics Workspace
Kusto Query Language (KQL)
Azure Monitor Alerts
Visual Studio Code
REST Client Extension
Application Description

The Flask application contains a /login endpoint that validates user credentials.

Successful login attempts generate a log entry containing:

LOGIN_SUCCESS

Failed login attempts generate a log entry containing:

LOGIN_FAILED

These logs are collected by Azure Monitor and stored in a Log Analytics Workspace for analysis.

Deployment

The application was deployed to Azure App Service using Azure CLI.

Application URL
https://YOUR-APP-NAME.azurewebsites.net
Screenshot – Deployed Application

(Insert Screenshot Here)

Monitoring Configuration

A Log Analytics Workspace was created and connected to the Azure App Service through Diagnostic Settings.

Enabled log categories:

Console Logs
HTTP Logs
Screenshot – Diagnostic Settings

(Insert Screenshot Here)

Log Generation

A test-app.http file was created and used to generate both successful and failed login attempts.

Example failed login request:

POST https://YOUR-APP-NAME.azurewebsites.net/login
Content-Type: application/json

{
    "username": "admin",
    "password": "wrongpassword"
}
Screenshot – HTTP Test Requests

(Insert Screenshot Here)

KQL Query

The following KQL query was used to detect excessive failed login attempts:

AppServiceConsoleLogs
| where ResultDescription contains "LOGIN_FAILED"
| summarize FailedAttempts = count() by bin(TimeGenerated, 5m)
| where FailedAttempts > 5
| order by TimeGenerated desc
Query Explanation
Filters log entries containing LOGIN_FAILED.
Groups events into 5-minute intervals.
Counts the number of failed login attempts during each interval.
Returns results when more than five failed login attempts occur within five minutes.
Displays the newest results first.
Screenshot – KQL Query

(Insert Screenshot Here)

Screenshot – KQL Query Results

(Insert Screenshot Here)

Alert Rule Configuration

An Azure Monitor Alert Rule was created using the Log Analytics Workspace as the scope.

Alert Settings
Setting	Value
Scope	Log Analytics Workspace
Query	Failed Login Detection Query
Measure	Table Rows
Evaluation Frequency	1 Minute
Aggregation Granularity	5 Minutes
Threshold	Greater Than 5 Failed Attempts
Severity	3

An Action Group was configured to send email notifications when suspicious login activity is detected.

Screenshot – Alert Rule Configuration

(Insert Screenshot Here)

Screenshot – Action Group Configuration

(Insert Screenshot Here)

Screenshot – Alert Triggered

(Insert Screenshot Here)

What I Learned

During this lab, I learned how to:

Deploy a Python Flask application to Azure App Service.
Configure Azure Monitor and Diagnostic Settings.
Collect and analyze application logs using Log Analytics.
Write Kusto Query Language (KQL) queries to identify suspicious activity.
Create Azure Monitor alert rules to automate threat detection.
Use Action Groups to generate email notifications when security events occur.
Challenges Faced

Some Azure resources were affected by subscription policy restrictions that limited available deployment regions. Additional troubleshooting was required when configuring monitoring resources and alert notifications.

Another challenge was identifying the correct Log Analytics table that stored the application logs before creating the final KQL query.

Real-World Improvements

In a production environment, the detection logic could be improved by:

Tracking failed login attempts by IP address.
Detecting credential stuffing attacks across multiple accounts.
Integrating alerts with Microsoft Sentinel or a SIEM platform.
Blocking suspicious IP addresses automatically using Azure WAF or Azure Firewall.
Adding geolocation-based anomaly detection.
Implementing account lockout policies after repeated failed login attempts.
Demo Video

YouTube Video Link:

PASTE_YOUR_YOUTUBE_LINK_HERE

The video demonstrates:

Application deployment to Azure App Service
Login activity generation
Log inspection in Azure Monitor
KQL query execution
Alert rule configuration
Alert triggering and notification
Repository Contents
app.py
requirements.txt
test-app.http
README.md
Author

Sara Mirzaei