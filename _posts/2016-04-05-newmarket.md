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
excerpt: Microsoft teamed up with NewMarket International on November of 2015 to help them begin a DevOps journey, assess where they could make improvements with the most impact, and implement recommendations to better deliver value to their customers.
---
##NewMarket DevOps Hackfest
Microsoft teamed up with NewMarket International on November of 2015 to help them begin a DevOps journey, assess where they could make improvements with the most impact, and implement recommendations to better deliver value to their customers.  The DevOps practices which were fully or partially implemented during this hack were:

- Continuous Integration
- Automated Testing
- Configuration as Code

In addition, this hackfest sparked NewMarket to start a permanent DevOps transformation involving the non-technical aspects such as culture, roles, and shared metrics changes.

The core hack team included:

- Jay Hilliard – Team Development Lead, NewMarket
- Jason Duso - Release Manager, NewMarket
- Dave Flanders - QA Lead, NewMarket
- Johnathan Govednik - Cloud Automation Engineer, NewMarket
- David Tesar (@dtzar) - Senior Technical Evangelist, Microsoft
- Gil Isaacs - Senior Software Development Engineer, Microsoft

##Customer Profile

NewMarket is an enterprise that delivers business solutions for hospitality organizations worldwide.  The company has over 40,000 installations, 150,000 users, and is used in 154 countries. NewMarket offers solutions for sales and marketing automation, catering and events, workflow management, and other areas for informing hospitality professionals. One of their web applications, NewMarket Web Services (NWS) e-Rest API, has new features that are released for it every three months and encompasses multiple products.

##Problem Statement

NewMarket involved multiple manual hand-offs to build and test code. In the development phase at NewMarket, developers locally built code then checked in changes to Team Foundation Version Control (TFVC) and manually interacted with a release engineer for them to build the code at the release engineer's availability. Automated and manual tests were run at a two-week regression cycle. 

Deployments were also manual and caused increased lead times. A developer communicated with a release engineer or IT to manually deploy a feature to a development environment, typically taking around four days per request to deploy to development or QA. Deployments to staging and production were also manual and could take up to a week for each deployment, with patches falling outside of the process. InstallShield installers were used in the staging environment, but added complexity to the deployment. When manual deployments to environments occurred, there would often be configuration-related issues causing problems. In total, the lead time for deployment from development to production was over ten weeks long and this assumed there were no problems and they only released a single feature.

## Solution, steps, and delivery ##

###Starting from where you are: Value Stream Mapping

In order to know where they were headed and where the biggest improvements could be made, NewMarket needed a realistic assessment of where they were at. It was key to understand how every team played a part in the current processes from conception of a feature until the feature reaches a customer's hand in production. The value stream mapping exercise identified the following, from development to production: 

- Each step involved in each environment and the roles of specific teams.
- Tools or products used for each step.
- The estimated lead times for each step, as well as per environment.

There were numerous opportunities identified, but it was decided to focus on the following areas for improvement during the Hackfest: 

- Automated build and testing with **TFS 2015** would save at least two hours per build of lead time per build, as well as be a foundation to enable other DevOps practices such as automated testing and continuous deployment.
- A two week test regression cycle was hard set into the release process. ~80% of the tests run during this cycle had the potential to be run automatically with the new build system.  Automatically running the tests nightly could provide justification to eliminate the hard-coded two week regression cycle and significantly reduce the overall lead time and provide faster feedback to developers.
- The InstallShield process to deploy builds and configure the virtual machine was a manual process taking at least an hour every time there was a new build, so **PowerShell DSC** provided an excellent way to automate the install and enforce configuration of the virtual machines where the app needed to run. 

###Continuous Integration and Automated Testing with TFS 2015

NewMarket had recently upgraded their TFS 2012 server to TFS 2015, but had no plans on using the build system.
The first part of the Hackfest implemented a new build definition with a continuous integration (CI) trigger. The build would run every time a check in occurred from a developer and compile the code with MSBuild.

The continuous integration build process included the following steps: 

- Compile the code
- Run unit tests against the code
- Produce the *ApplicationCode* package.
- Produce the *DeploymentScripts* package (from a folder within the repo).

UPDATE: add screenshot

By enabling this build definition, the bottleneck for waiting for manual builds was eliminated. They removed the custom-built website running on a VM for developers to request a new build and replaced that with simply being able to access the TFS build service website.  Developers also received immediate feedback from the automated builds if their changes broke the build and were able to address the issues quickly without reacquiring context a week later. This was important for NewMarket to better embrace DevOps practices with agility.  

Next, the automated tests that were typically run by QA later in the process were introduced as Visual Studio Test tasks in automated builds. The shorter tests (such as unit tests and a smaller list of feature tests) were added into the continuous integration build, and the longer tests were added into a scheduled nightly build. Previously, a manual process by quality assurance was involved to categorize tests, but NewMarket could categorize in test classes and test methods and include that as part of the build definition and quality of the build. 

By running tests earlier in the development process with the builds, NewMarket was able to identify defects and bugs earlier in the process, remove the two-week regression cycle, and ultimately lead to a better product to customers. 

###Configuration as Code with Powershell DSC

NewMarket created a PowerShell DSC script to configure the virtual machine dependencies and install the build. This helped to eliminate the delay of a release engineer to manually click through a GUI InstallShield wizard to do the same steps.

The PowerShell DSC script had the following steps: 

- Install IIS and dependent services
- Set registry permissions and configure registry settings

UPDATE: possibly share actual script

The process of using PowerShell DSC and PowerShell to deploy the build and configure the virtual machines began the conversation of a repeatable procedure across the development, staging, and production environments. Reliable deployments removed manual delays and configuration-related issues. By running deployment scripts with PowerShell DSC, InstallerShield installers were no longer needed. This process also opened the possibility of allowing developers and testers to create their own development and QA environments hosted in Azure rather than break the shared development environment. 

> "If we could quickly spin up new environments required to develop against e-Rest API, this would be beneficial to over 75% of those developers." - Jay Hilliard, development team lead

## Conclusion ##
By automating continuous integration builds with Team Foundation Server (TFS) 2015, ensuring consistent configuration of the virtual machines with PowerShell Desired State Configuration (DSC), and removing the two week regression test cycle in and automating functional tests, the lead time from development to production is reduced by 25% and allows for a repeatable build/test/deploy process with opportunities for improvement in other areas of development.

The original lead time from development to deployment: **10 weeks, 1 day, and 10 hours**. The new lead time after a week of improvement with adding continuous integration builds, nightly tests, and automated deployments: **7 weeks, 5 days, 10 hours**. This reduced the best case lead time by **13 days, 4.5 hours**, the lead time with bug fixes by **4 days**.

Furthermore, feedback was much faster with build/test/deploy with more tests and automatically running nightly versus 2 weeks, and automatically building and deploying versus ~8 hours savings per bug discovered.

NewMarket now has buy-in from upper management to spend real time in the next program increments with DevOps backlog items in the sprints following for development, quality assurance, operations, and release engineering. 
###General Lessons###

When teams work together towards a larger common goal amazing opportunities for collaboration happens, blurring the lines of roles and responsibilities.  For instance, members of different teams of NewMarket made contributions in non-typical areas of responsibility.  A developer wrote code for the PS DSC script which helped eliminate the InstallShield installers, operations and test engineers helped with creating build definitions and build agents.

It is very uncommon for companies to wipe out their current software development lifecycle process, so it is pertinent to start where you are. 

Begin the conversation by running the value stream exercise with team members across different disciplines and show the current process to reveal pain points and bottlenecks. 

Although the PowerShell DSC details may not align perfectly to every situation, the principles and tasks are generally the same and may be applied in various environments using Windows Server regardless of whether running on bare metal, on-premises, or in Azure.

##Resources

[Overview of Team Foundation Build 2015 ](https://msdn.microsoft.com/en-us/library/vs/alm/build/feature-overview)

[Implementing Continuous Delivery with Build vNext and Release Management](http://blogs.msdn.com/b/charles_sterling/archive/2015/06/22/ci-and-cd-hands-on-lab.aspx)

[Testing in Continuous Integration and Continuous Deployment Workflows](https://blogs.msdn.microsoft.com/visualstudioalm/2015/05/29/testing-in-continuous-integration-and-continuous-deployment-workflows/)

[Windows PowerShell Desired State Configuration Overview](https://msdn.microsoft.com/en-us/powershell/dsc/overview)