# Download Starred Email Threads Script
#### Author: Matthew Dillon
#### Last Edited: 2025-06-27

---

## Table of Contents
- [Purpose](#purpose)
- [Background](#background)
- [Project Relevance to Data Science](#project-relevance-to-data-science)
- [Project Constraints](#project-constraints)
- [Tools Leveraged](#tools-leveraged)
- [Basic Solution Description](#basic-solution-description)
- [Features](#features)
- [Detailed Script Methodology](#detailed-script-methodology)
- [Triggers](#triggers)
- [Script Requirements](#script-requirements)
- [Customization](#customization)
- [Personal Deployment Results](#personal-deployment-results)
- [Future Enhancements](#future-enhancements)
- [Links](#links)
  
---

## Purpose
This project was developed to automatically download "starred" email threads as well as their attachments to prevent deletion under a new corporate email retention policy. It leverages Google Apps Script as an automated solution to maintain long-term email record-keeping for personal or professional use.


## Background
In early 2024, the corporate overlords of the company I was working for at the time sent a notification to all 300,000+ employees that a new email retention policy would go into effect on April 21, 2024. This new policy would delete all email threads older than 365 days, and would continue to do so on a rolling basis in perpetuity. I did not feel that this policy was fair to employees like myself who, albeit infrequently, rely on information memorialized in historic email threads. Furthermore, I felt that this policy is even more outrageous to subsets of the corporation such as legal teams, financial teams, and C-suite employees who will surely have very important documentation saved in the archives of their email inbox. Additionally, the corporate overlords did not provide any reasonable solution to download important email threads in bulk before deletion, meaning users had to manually download threads one by one.

In a blatant act of corporate disobedience, I designed and implemented a solution for my own work email account via Google Apps Scripts to automatically forward emails at risk of deletion back into my inbox and archive them appropriately. Unfortunately, my director would not allow me to share or publish this solution to any colleagues. Therefore, I decided to design yet another script which approached the problem in a different way while adhering to the new corporate policy: automatically downloading any starred email threads and all attachments.


## Project Relevance to Data Science
While this project doesn't leverage traditional ML/AI techniques that are usually synonymous with data science, it exemplifies many of the core tenets of data science such as:
- Automation via programming (JavaScript in Google Apps Script)
- Leveraging APIs to connect data sources
- Designing and implementing self-sufficient workflows
- Demonstrating creative solutioning and problem-solving within the confines of data and environmental constraints


## Project Constraints
The only major constraint that this project faced was the fact that our corporate overlords were pushing all Google products, and as a result the entire organization uses Gmail. Therefore, my solution would need to be able to integrate with Gmail and Google Drive, and unfortunately would not be applicable to Outlook users.


## Tools Leveraged
Since I had previously implemented a similar solution to this policy for my own personal Google account, I will continue to leverage Google Apps Scripts for this project. Google Apps Scripts (GAS) is Google's application development platform that allows any user with a Google account to create robust scripts in JavaScript programming to automate processes and create streamlined workflows. GAS has the incredibly powerful ability to integrate with the entirety of the Google Workspace products via individual APIs, meaning that users' scripts can seamlessly interact with Gmail, Google Drive, Google Sheets, Google Forms, Google Calendar, Google Docs, etc. etc.


## Basic Solution Description
At a high level, my GAS code will iterate through all email threads that have been "starred" by the user. If the thread has been inactive for over 180 days, the script will then download the email thread as both a .pdf file and a .eml file, as well as any attachments on the thread, and logically archive these newly created files into the user's Google Drive. With this functionality as well as a daily trigger set for an early morning hour, the script performs seamlessly in the background, all the while saving the user's email threads that have been identified as important.


## Features
- Automatically identifies starred email threads
- Calculates the number of days elapsed since the last activity on the thread
- Downloads the email thread (.pdf and .eml) if the last activity exceeds 180 days (thread is considered inactive)
- Downloads all attachments on the thread
- Logically organizes downloaded files into the user's Google Drive
- Apply a custom Gmail label to threads that have been automatically downloaded


## Detailed Script Methodology
My GAS script begins by connecting to the Google account associated with the current script user. Next, the script will search for the "Downloaded Emails" folder in the user's Google Drive as well as "EML Files" and "Attachments" subfolders - if these subfolders do not exist, the script will automatically create them. This is where the downloaded email thread files will be pushed later on in the script.

The script will then iterate via pagination through the user's email threads in their inbox and/or in their custom labels and identify email threads that have been starred by the user.

For each qualifying email thread, the script will calculate the number of days that have elapsed since the most recent email message on the thread was either sent or received - this will be considered the number of days since last activity on the thread. If the most recent message is fewer than 180 days old, the script will do nothing and will iterate to the next thread. If the most recent message is greater than or equal to 180 days old, the email thread will then be automatically downloaded as a .pdf file. This newly created .pdf is named by extracting the date and the subject line of the *first* email on the thread for ease of discovery. The .pdf file will now be pushed to the "Downloaded Emails" Google Drive folder discussed above.

Next, the script will also create a .eml file of the thread, which will be downloaded and pushed to the aforementioned "EML Files" subfolder of the overarching Google Drive folder. The naming convention for the .eml file is exactly the same as the .pdf file, concatenating the date and subject line of the *first* email message separated by a hyphen. After this, any attachments on the email thread will be identified and downloaded to the "Attachments" subfolder, keeping the file type and file name of the attachment as is.

Once all files and attachments for a thread have been created, the script will automatically apply a "Downloaded Email" custom Gmail label to the thread to identify that it has been downloaded. If the label does not already exist, the script will automatically create it.

But what happens if an email thread gets new messages after it has already been downloaded? These new threads won't appear in our downloaded file, and this is problematic because they could include important information. To address this legitimate concern, the script will iterate through the subset of the email threads with the "Downloaded Email" Gmail label applied. If more than 180 days has elapsed since the most recent message on the thread, we can infer that no new email messages have been received on the thread and continue to the next without taking additional action. However, if the most recent message on the thread is fewer than 180 days old, this means the thread has new activity and the script will **remove** the "Downloaded Email" label from the thread. This will essentially "reset" the email thread and will download a more updated version once the thread becomes inactive again, differentiated from the original files by a different date in the file names.

The script concludes with a dynamic message to the built-in log specifying the number of email threads and attachments that have been downloaded, or a message relaying that zero threads were downloaded.


## Triggers
Once the script was complete, I further leveraged GAS's trigger function to set a daily trigger for this script to automatically run. I personally chose for the script to run between the hours of 2:00-3:00am MST to ensure I will not be actively sending emails during its execution. The use of a trigger allows this script to be self-sufficient, automatically downloading all qualifying email threads and attachments in the background.


## Script Requirements
The requirements to leverage this script on your own account are as follows:
1. Active Google account
2. Google Apps Script Gmail API (Editor > Services > Gmail API > Add)
3. Google Apps Script Drive API (Editor > Services > Drive API > Add)
4. Trigger (Triggers > Add Trigger > ...)
5. Authorization (allows the script to interact with your Gmail account and Drive account via the aforementioned APIs; only required the first time the script is run)

To leverage this script for personal use, navigate to [Google Apps Script](https://script.google.com/home), create a new project, and copy/paste the attached GAS script. Script customization is discussed below.


## Customization
The script has been configured such that it connects to the user's Google account, therefore manually specifying the email address to use is not necessary. 

The arbitrary 180 day benchmark I chose as an "inactive" email thread can be adjusted by the user using the *daysEmailThreadInactive* variable on line 44 of the script. The name of the "Downloaded Emails" Google Drive folder can also be edited by the user using the *downloadedEmailsFolderName* variable on line 27.


## Personal Deployment Results
Once the script was finalized, I showed the code and the process to my director at the company to advertise the solution I had developed, which not only addresses the new corporate email retention policy but also alleviates any concerns he had about sharing my other automated email forwarding script. He was quite impressed with the abilities of this new script, and he scheduled a meeting for me to showcase it to the corporate director of compliance at the organization.

Even though I had my director's support and the corporate compliance team was really excited about the prospect of this solution, steps to implement it across the company were unfortunately never pursued.

I do not personally have this script triggered and deployed for my personal account because I continue to use my other automated email forwarding script. However, the script was tested and presented many times with a 0% error rate, even in live demonstrations over Zoom.


## Future Enhancements
While I do not plan on enhancing this script any further because I leverage an alternative solution, here are some ways in which the script could be enhanced in future versions:
1. Create an actual script deployment so that the script does not need to be copied/pasted into a user's personal GAS project to function
2. Improve the Google Drive folder so that the user can choose the naming convention for the folder and subfolders which the downloaded files will be pushed to
3. Change the 180 day "email inactivity" metric into a parameter that the user can more easily specify at the beginning of the script
4. Enhance the code to consolidate duplicated efforts (i.e. iteration through the "Downloaded Emails" thread *after* iteration through all email threads)
5. Identify ways in which the script can handle email attachments that are locked, confidential, password-protected, etc.

---

## Links
[Github repository link](https://github.com/matthewdillon1/Automated-Email-Forwarding-Script)

[automated-email-forwarding-script.js](https://github.com/matthewdillon1/Automated-Email-Forwarding-Script/blob/main/automated-email-forwarding-script.js)
