---
layout: post
title: Attacking and Defending Azure AD Cloud - Beginner's edition
author: RandomByte
date: 26-06-2021
---

A month ago, I decided to take the "Attacking and Defending Azure AD Cloud: Beginner's Edition" bootcamp by [Pentester Academy](https://bootcamps.pentesteracademy.com/course/ad-azure-jun-21). While I have participated in some web app penetration tests and assessed other technologies (Kubernetes, Azure DevOps, etc), I never had the chance to delve into proper red teaming. Therefore, this course gave me a first test of what red teaming feels like and it was a very interesting experience. This post will be divided in two parts, each having their own sections: 

- [The Course](#section1)
	- [Content](#content)
	- [The lab](#lab)
- [The Exam](#section2)
	- [Reporting](#report)

## <a name="section1"></a>The Course
Overall, I found the course to be very comprehensive and satisfying. Moreover, although I do have some experience in Microsoft Azure, there were several surprises troughout the course that changed some of the notions I had. I also really enjoyed [Nikhil Mittal's](https://twitter.com/nikhil_mitt) teaching. He was very welcoming and encouraging with questions and students' comments during the course. This proved to be really useful as some of the course attendants were really knowledgeable, so I was able to get very interesting tips from them. 

### <a name="content"></a>Content
Notice that while the course is organized in four modules that, in a sense, follow the logical path you would encounter in a real life cyber attack, given the breadth of Azure and the use of several kill chains (i.e., the structure of the individual cyber attacks in the course), it is sometimes necessary to jump back and forth between modules. The material very clearly states this situation, but it is useful to keep it in mind to approach the course.


1. **Module 1:** Covers an introduction to Microsoft Azure architecture & Azure Active Directory, unauthenticated enumeration, and initial access attacks ranging from password sprays to Server-Side Template Injections (SSTI) and other types of vulnerabilities you may find in Azure services. 
2. **Module 2:** Covers authenticated enumeration of Azure services such as storage accounts & blobs, key vaults, automation accounts, deployment templates, etc and touches into privilege escalation: Azure Role-Based Access Control (RBAC), Azure AD Roles, Azure Subscriptions, etc. 
3. **Module 3:** Covers lateral movement techniques such as Pass-the-PRT, Pass-the-Certificate, how to move across the tenant, and between cloud and on-premises environments.
4. **Module 4:** Covers data mining, the defenses that are used in Azure such as Privileged Identity Management (PIM), Privileged Access Management (PAM), Conditional Access Policies, Just In Time Access (JIT), security center, etc. When discussing these defenses, the course also discussed some common ways in which these defenses can be bypassed.  

In general terms, I found the content to be very complete and to touch upon a lot of Azure services that are commonly used and how they can be exploited. Moreover, it was interesting that all the theoretical explanations were backed up with hands-on demonstrations that showed how the attacks were executed. This brings us to the next topic: the lab.

### <a name="lab"></a> The Lab
As a companion to the 3-hour teaching sessions, the bootcamp comes accompanied with a lab that allows the students to practice the techniques given by the instructor. This format is very useful because it allows students to come to the sessions with questions whenever they are stuck with the lab. These questions are then directly answered during the sessions. 

In general terms, the lab is composed of a number of _learning objectives_, which correspond to chunks of the topics studied during the sessions. These learning objectives can be used as milestones that allow the student to take in what was explained. Personally, I would not recommend to do the lab at the same time as the sessions, as you would be missing the interesting discussions that may arise. Ideally, you would sit after each lectures to clear the learning objectives. In this way, you can be sure that you learned and understood what was explained.


## <a name="section2"></a>The Exam
The exam will grant you the "Certified Az Red Team Professional" (CARTP) certification. When taking the exam, you will be given 25 hours for hands-on testing plus 48 hours of reporting. This means that the exam lab will only be accessible for 24 hours (plus 1 hour to compensate for the time the exam lab takes to create). Regarding the exam lab itself, I will not share too many details as I do not want to spoil anything. In general terms, the exam follows a "red team operation" format where you are given a number of objectives to compromise and an initial access position. It is up to you then to compromise the targets and finish all the objectives. 

In terms of difficulty, I will say that everything needed to clear the exam is in the course. This is why properly understanding and taking in the techniques taught in the course is important. Thus, it is really important to take notes and properly understand the explanation of each command and each tool being used during the lessons. 

### <a name="report"></a> Reporting
Besides compromising the objectives, reporting is, in my opinion, the most important aspect of the exam. In my case, I tried to divert myself from my usual disorganized note-taking and tried to document everything being as organized as possible. This really helped me to gather all the needed information after the exam lab was finished.  In terms of tooling, I used Markdown (Zettlr) and OneNote for my notes. Then, wrote the report on Markdown, and converted into a `.tex` file to finish it using LaTeX. In hindsight, I will probably try to use some other tools when attempting different exams that require reporting, as using LaTeX was quite time-consuming.