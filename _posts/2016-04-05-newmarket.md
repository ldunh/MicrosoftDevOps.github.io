---
layout: post
title:  "CI and Automated Testing with NewMarket"
author: "David Tesar"
author-link: "#"
#author-image: "{{ site.baseurl }}/images/authors/photo.jpg"
date:   2016-04-05 20:13:28
categories: DevOps
color: "blue"
#image: "{{ site.baseurl }}/images/imagename.png" #should be ~350px tall
excerpt: In this DevOps Hackfest, Microsoft teamed up with NewMarket International to help them begin on a DevOps journey and determine where they could make improvements with the most impact to deliver value to their customer.
---
#Learnings from a hackfest with NewMarket
In this DevOps Hackfest, Microsoft teamed up with NewMarket International to help them begin on a DevOps journey and determine where they could make improvements with the most impact to deliver value to their customer.  The DevOps practices which were fully or partially implemented during this hack were:

- Continuous Integration
- Automated Testing
- Configuration as Code

In addition, this hackfest sparked the start for Newmarket to make a true DevOps tranformation involving the non-technical aspects such as culture, roles, and shared metrics changes.

##Customer Profile

Newmarket is an enterprise that delivers business solutions for hospitality organizations worldwide.  The company has over 40,000 installations, 150,000 users, and is used in 154 countries. Newmarket offers solutions for sales and marketing automation, catering and events, workflow management, and other areas for informing hospitality professionals. One of their web applications, Newmarket Web Services (NWS) e-Rest API, has new features that are released for it every three months and encompasses multiple products.

Before tackling the technical aspects of builds and deployments, a conversation with members from the development, quality assurance, operations, and release management teams was imperative. 

##Problem Statement

Newmarket involved multiple manual hand-offs to build and test code. In the development phase at Newmarket, developers locally built code then checked in changes to Team Foundation Version Control (TFVC) and manually interacted with a release engineer for them to build the code at the release engineer's availability. Automated and manual tests were run at a two-week regression cycle. 

Deployments were also manual and caused increased lead times. A developer communicated with a release engineer or IT to manually deploy a feature to a development environment, typically taking around four days per request to deploy to development or QA. Deployments to staging and production were also manual and could take up to a week for each deployment, with patches falling outside of the process. InstallShield installers were used in the staging environment, but added complexity to the deployment. Every time a manual deployment to any environment occurred, there would be a configuration-related issue. In total, the lead time for deployment from development to production was over ten weeks long and this assumed there were no problems and they only released a single feature.

## Solution, steps, and delivery ##

###Starting from where you are: Value Stream Mapping

In order to know where they were headed and where the biggest improvements could be made, Newmarket needed a realistic assessment of where they were at. Taking time to whiteboard with everyone how a feature was released, was key to understanding how each team played a part in the current structure and how they will need to work together to improve the deployment pipeline. 

The value stream mapping exercise identified the following, from development to production: 

- The current setup for iterations and work items.
- Each step involved in each environment and the roles of specific teams.
- The estimated lead times for each step, as well as per environment.

![](media/Newmarket-value-stream-before.jpg)

Upon calculating the lead time for the entire deployment process from development to production, the conclusions were made that: 

- Automated build and testing in **TFS 2015** would save at least two hours per build of lead time, as well as be a foundation to enable other DevOps practices such as automated testing and continuous delivery.
- The install shield process to deploy builds and configure the virtual machine was a manual process taking at least an hour every time there was a new build, so **PowerShell DSC** provided an excellent way to automate the install and enforce configuration of the virtual machines where the app needed to run. 

This roadmap set the stage for the main areas to focus on during the Hackfest. 

###Continuous Integration and Automated Testing with TFS 2015

Newmarket had recently upgraded their TFS 2012 server to TFS 2015, but was not using the new build system yet. 

The first part of the Hackfest implemented a new build definition with a continuous integration (CI) trigger. The build would run every time a check in occurred from a developer and compile the code with MSBuild.

The continuous integration build process included the following steps: 

- Compile the code
- Run unit tests against the code
- Produce the *ApplicationCode* package.
- Produce the *DeploymentScripts* package (from a folder within the repo).

By enabling this build definition, the bottleneck for waiting for manual builds was eliminated. Developers also received immediate feedback from the automated builds if their changes broke the build and were able to address the issues quickly without reacquiring context a week later. This was important for Newmarket to better embrace DevOps practices with agility.  

Next, the automated tests that were typically run by QA later in the process were introduced as Visual Studio Test tasks in automated builds. The shorter tests (such as unit tests and a smaller list of feature tests) were added into the continuous integration build, and the longer tests were added into a scheduled nightly build. Previously, a manual process by quality assurance was involved to categorize tests, but Newmarket could categorize in test classes and test methods and include that as part of the build definition and quality of the build. 

By running tests earlier in the development process with the builds, Newmarket was able to identify defects and bugs earlier in the process, remove the two-week regression cycle, and ultimately lead to a better product to customers. 

###Deploying reliably: Configuration as Code with Powershell DSC

Newmarket created a PowerShell DSC script to configure the virtual machine dependencies and install the build. This helped to eliminate the delay of a release engineer to manually click through a GUI InstallShield wizard to do the same steps.

The PowerShell DSC script had the following steps: 

Members of different teams cross-trained to contribute to deployment automation. A developer wrote code for the PS DSC script which helped eliminate the InstallShield installers, operations and test engineers helped with creating build definitions and build agents.

The process of using PowerShell DSC and PowerShell to deploy the build and configure the virtual machines began the conversation of a repeatable procedure across the development, staging, and production environments. Reliable deployments removed manual delays and configuration-related issues. By running deployment scripts with PowerShell DSC, InstallerShield installers were no longer needed. This process also opened the possibility of allowing developers and testers to create their own development and QA environments hosted in Azure rather than break the shared development environment. 

> "If we could quickly spin up new environments required to develop against e-Rest API, this would be beneficial to over 75% of those developers." - Jay Hilliard, development team lead

## Conclusion ##
By automating continuous integration builds with Team Foundation Server (TFS) 2015 and deploying reliably with PowerShell Desired State Configuration (DSC), the lead time from development to production is reduced by 25% and allows for a repeatable build/test/deploy process with opportunities for improvement in other areas of development.

The original lead time from development to deployment: **10 weeks, 1 day, and 10 hours**. 

The new lead time after a week of improvement with adding continuous integration builds, nightly tests, and automated deployments: **7 weeks, 5 days, 10 hours**.

This reduced the best case lead time by **13 days, 4.5 hours**.

This reduced the lead time with bug fixes by **4 days**.

The overall lead time reduction was **17 days, 4.5 hours**.=

Feedback was much faster with build/test/deploy with more tests and automatically running nightly versus 2 weeks, and automatically building and deploying versus 8 hours savings per bug. 

![](media/Newmarket-value-stream-after.png)

Newmarket now has buy-in from upper management to spend real time in the next program increments with DevOps backlog items in the sprints following for development, quality assurance, operations, and release engineering. 
###General Lessons###

It is very uncommon for companies to wipe out their current software development lifecycle process, so it is pertinent to start where you are. 

Begin the conversation by running the value stream exercise with team members across different disciplines and show the current process to reveal pain points and bottlenecks. 

Although the PowerShell DSC details may not align perfectly to every situation, the principles and tasks are generally the same and may be applied in various environments using Windows Server regardless of whether running on bare metal, on-premises, or in Azure.

##Artifacts

[Overview of Team Foundation Build 2015 ](https://msdn.microsoft.com/en-us/library/vs/alm/build/feature-overview)

[Implementing Continuous Delivery with Build vNext and Release Management](http://blogs.msdn.com/b/charles_sterling/archive/2015/06/22/ci-and-cd-hands-on-lab.aspx)

[Testing in Continuous Integration and Continuous Deployment Workflows](https://blogs.msdn.microsoft.com/visualstudioalm/2015/05/29/testing-in-continuous-integration-and-continuous-deployment-workflows/)

[Windows PowerShell Desired State Configuration Overview](https://msdn.microsoft.com/en-us/powershell/dsc/overview)