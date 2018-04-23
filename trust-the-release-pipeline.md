# Overview

> **Please Note: This paper is unpolished and at times opinionated and rant-y, it is my expectation to mold this into something polished and presentable based on questions and feedback received so please, do send that and offer your perspectives.**

This paper aims to provide some real world and practical context to implementing a Release Pipeline for Infrastructure. A Release Pipeline is a DevOps process traditionally used for deploying software but is equally applicable to infrastructure when managing via Infrastructure-as-Code.

It does not focus on the technical steps, instead, on the process, challenges and high level thinking required to influence this change within an Enterprise organization. 

What is outlined in this paper is a suggested approach for how to adopt a Release Pipeline for infrastructure within an Enterprise, ITIL environment. The content consists of experience collected together from different customers and conversations, with some specifics changed to prevent the identification of those organizations.

> **This should not be treated as an official Microsoft document or any official guidance from Microsoft.** This paper reflects my own experience, thinking, approach and opinions. Official Microsoft documentation, guidance and assistance may be available.

It is important to note that the Release Pipeline is a concept. The implementation of it can differ dramatically depending on the Team implementing it, the people involved, the requirements of the organization, the operating environment and culture of that organization. TWhat is detailed in this document will not be agreed with by everybody and will not make sense in every environment. The intention is to provide you with at least a starting point or a different perspective to help you along your own journey.

# Why am I writing this Paper?

I am writing this paper because I spent a long time on my Infrastructure-as-Code journey doing the wrong thing. Like most technical folks, my view of Infrastructure-as-Code on the Microsoft stack was doing Desired State Configuration. While this is a component of the Infrastructure-as-Code approach, it is by no means the destination. In fact, I came to learn it is the least important element. What I learned to be most important was the delivery mechanism, which I gave little thought to until I had DSC doing incredible things.

I liken this to having spent time building up the pizza-making capability of a delivery service only to be making incredible pizzas that no one was eating - because I couldn't deliver them! Instead, I wish I spent my time building up capability to deliver a plain cheese pizza in 20 minutes, consistently, to as many people who ordered, before I built out my menu.

What I found to be the most difficult is to get an infrastructure-as-code approach accepted by the Enterprise, because this was a change to the norm, this was automation that people had little reason to trust. What ensued was months of building release automation, workflows, feedback loops and so forth that, if we focused on at the start would have made ramping up the DSC code easier.

People often asked me "can DSC do … x…" or "can DSC do … y…". Here's the thing - "Yes". DSC can do anything you want it to. DSC is code, code you can write yourself. There may not be Resources readily available but DSC can do anything you want. If you find yourself asking or being asked this question, rather than trying to prove what it can or cannot do, stop yourself. Focus on "how do I deliver my DSC code" and you'll find that is time best spent. Once you break down the barriers to using it in your enterprise, you can start to flow fast.

It is my hope that this paper helps you break down those barriers.

# What is the Release Pipeline and what does it mean for Infrastructure?

There is an excellent whitepaper written by Michael Greene and Steven Murawski on the Release Pipeline for Infrastructure called [The Release Pipeline Model](https://docs.microsoft.com/en-us/powershell/dsc/whitepapers), it is highly recommended reading. In short, the Release Pipeline is a methodology for how we can take code and deliver it into a production scenario where it can be used, through an automated Testing and Deployment solution.

From the whitepaper, the Release Pipeline concept is comprised of 4 stages:

![Stages of a Release Pipeline](/img/release-pipeline-phases.jpg)

Release Pipelines can essentially be viewed as automated quality control for code.

When we think about code from an Infrastructure perspective, these can be scripts, modules, runbooks, Desired State Configurations and the like. The code you deliver may just be code deployed as Runbooks for others to consume, or it may be the execution of DSC Configurations themselves to affect change on Production Servers.

Release Pipelines are typically used for software and PaaS/DevOps projects to deliver changes to code into working software, but from an Infrastructure perspective either as part of a DevOps process with an application or purely to manage infrastructure they have immense value.

By implementing a Release Pipeline for Infrastructure and bringing an existing service under code control, you can effectively eliminate manual changes. It is perfectly reasonable to expect:

* That you can start documenting the configuration of your service in Desired State Configuration code with Environment Files.
* That you can release the same configurations to different environments and get the same results.
* That you can make changes to environments by making a change to your configurations.
* That you can test the outcome of these changes as they are rolled out into your environments and get real time feedback on success or failures so you can take action.
* That you can have your whole infrastructure service completely captured in code that you can deploy or redeploy anywhere are any time.

That is the true definition of Agility. I have been around a number of customers who I listen talk about being Agile and they refer to being able to iterate using an Agile process. That, to me, is not being Agile. For me, true Agility comes from being able to pivot quickly and respond to new demands quickly with high quality and stability. To be in one cloud service today and be able to, at short notice, re-deploy your service into a new location or environment and to swing your users to that new environment seamlessly. To be able to hear about a security threat one morning and be ready to deploy mitigating changes by lunch without breaking a sweat.

That, to me, is true agility and that is completely reasonable to expect. It all relies on moving to a Release Pipeline model for infrastructure and that can be a challenge when it means affecting change within your organization.

![Generic Release Pipeline](/img/generic-release-pipeline.jpg)

# What is the challenge to adoption?

The challenge to adoption is generally down to a few key things:

1. **A gap in education and understanding** - In my experience I have seen technology teams working to understand this process, but the persons involved in the delivery chain for production changes are not. The technology teams then come up against the task of having to educate those other teams before they can adopt these new practices
2. **A lack of Trust** - The different stakeholders involved in the delivery process coming from an ITIL world typically have a distrust for each other. This comes from years of implementing mitigations and multiple processes following the outcome of failed change in the environment. It also comes from the Silo approach that a lot of organizations take. There is a funny image used to represent how Microsoft used to be with each team pointing weapons at each other. In fact, this is still a common thing among enterprise IT Teams.
3. **Reluctance to Change** - Change is hard, it usually brings instability, instability brings risk and ITIL organizations have spent a long time building complex processes that remove risk - sometimes at the cost of preventing change. When a gap in education about where you're going exists, and you have a lack of trust for the other teams involved, the suggestion of bringing potential instability and situations where you have to learn and trust each other into daily operations seems counter to what the organization has spent so long building.

When a technology operations team or engineering team is trying to change the way they manage and deliver infrastructure, these 3 things act as a barrier to getting over the line.

# Where does this typically apply?

You may be reading this and thinking that this doesn’t represent your scenario at all. That is awesome! You work in a much healthier organization than a number of other people, but hopefully there is still some elements of this paper which will be useful to you. The perspective this paper is written from is the following:

* Working in very large enterprises that are very ITIL or legacy driven
* Company cultures where Trust between different teams in the IT organization has eroded
* Companies that employ people who they do not necessarily invest in keeping their skills up to date or who don't necessarily invest in taking that initiative themselves
* The IT organization is largely resistant to change external to their own control
* Organizations that have built up drawn out change processes that may include:
    * Releasing a change across multiple environments with breaks in between
    * Change process requires you to state the change, how, rollback, success measurements
    * Security team have concerns/reservations about automation

If this describes you, then typically it is safe to assume you've hit a brick wall.

# What this paper assumes about you

If the previous section sounds like your scenario and you've run up against a bit of a wall with operationalizing DSC or you're only just starting off on your journey and wondering how to begin the assumption is that you're either learning or already know:

* How to build DSC configurations and separate Environment Data
* How to use Team Foundation Server/VSTS
* How to use Psake or InvokeBuild

Any code samples provided to support this paper will plug into these systems. If you're doing something different, that’s more than fine, this is about the process rather than the technology, there is just some adaptation you will need to do yourself.

# How do we integrate a Release Pipeline and use Infrastructure-as-Code?

There is no one-size-fits-all solution and every approach is going to be different, because every organization and every team is different. The approach I learned based on environments like the above was:

* Educate the stakeholders by Integrating into the current process. Prove the concept.
* Build Trust in the process by showing results and success
* Use your track record of proven methodology to show you can:
    * Reduce the timespan to deploy changes to the environment with low risk
    * Make infrastructure changes during business hours
    * Bring consistency to the Dev, Test, UAT and Production environments

By following these three phases and taking stakeholders on the journey with you, it generally leads to a more successful approach. This means focusing on the Release process first and less on the DSC side until later. Remember, DSC is not a solution in itself, it is a component of the overall solution of Infrastructure-as-Code. The primary component of which, is the Release Pipeline.

# The 3 Phases of the Process

![3 Phases](/img/3-phases.jpg)

## Phase 1: Prove the Concept

### Start with your PowerShell Code

Before starting a Release Pipeline to manage infrastructure, it is a good idea to get one implemented for your PowerShell code first. This brings with it a number of benefits:

1. It allows you to fully grasp the release pipeline concept
2. It allows you to start collaborating with others on code development
3. It allows you to demonstrate the concept to future stakeholders as a way for them to visualize the flow and understand what you are doing

When you start collaborating with others on code development from a central repository it will teach you some very key lessons for success with Infrastructure-as-Code. In my experience this is actually the most difficult part of Infrastructure-as-Code. Many people have grasped the high level concepts of what a Release Pipeline is and what the components are and how they fit together. In fact, I have seen for myself many architects with no code background whatsoever create attractive presentations and documents discussing how code is taken from a source repository and sent down a pipeline through testing and multiple systems before deploying into production. However, they have not grasped the process for how a team would make updates to that code. They have not grasped how a team would actually code their Tests and how those tests should actually execute. They just know "tests should be run" and "code should be iterated". Furthermore, I have seen for myself how this falls apart after the first demonstration of an Infrastructure change as code. When the next change is required, the process is missing, because too much thought and excitement went into this first pipeline!

To avoid falling into the same trap, the most important skill that can be learned is contributing to central, source controlled code (you can find resources on how to do this at the end of this paper).

A Release Pipeline for your PowerShell Modules might include the following:

* Source
    * A Source Code repository such as VSTS using Git
* Build
    * A Build Definition that would:
        * Run Unit Tests using Pester against the module
        * Test for Code Linting (PSScriptAnalyzer)
        * Test for module functionality
        * Compile ZIP files and Checksums of Modules and Scripts
* Release
    * Publish to an Internal or Public PowerShell Gallery
    * Run a Script to instruct Pull Servers or end nodes to download new versions of Modules

This is a very simple set up that will allow you to prove the concept for yourself.

![Example of an Artifact Release Pipeline](/img/artifact-management-example.jpg)

### Understand and demonstrate how you would deal with "Roll Back"

A key lesson to learn in this area is "Roll Back". With code, Roll Back is not such a good idea. We actually "fix forward". This is a very important concept to grasp when dealing with ITIL, Change Management and the like. A standard question will be "If a change fails, how will you roll back?". An expected answer might be "We will re-run the previous version of the code/package to deploy the previous settings". It's perfectly reasonable to expect this but there are two caveats:

1. If the failed release is the first one where a particular setting was introduced and you go back and deploy the previous code/package version, there is nothing there to "undo" the change you just made.
2. If there was something there to undo the change you just made, you have regressed to, for example, v1.0 and v1.1 is failed. You wouldn’t want to deploy a v1.1 again so when you release v1.2 you have a v1.1 in the way that you didn’t actually deploy (because you rolled back). This can become confusing.

My preferred way for dealing with "Roll Back" is to first align the terminology and call it "regression". You will "regress" to previous settings, configuration or functionality. This requires some planning and these steps too, allow you to build trust with stakeholders:

1. **Plan small releases** - at no point, especially on the first release of your code, should big bang changes apply. The only time this can happen is on newly built systems when you are applying a configuration that brings them up to the same configuration as your Production systems are currently running. [The DevOps Handbook](https://www.amazon.com/DevOps-Handbook-World-Class-Reliability-Organizations/dp/1942788002) provides details of this in practice.
2. **When bringing a new setting under control for the first time, it should not make a change to that setting** - it should only ensure the default setting (which should be the current setting, if it's not, use the current setting) is applied. This should be released so not to make a change. This means, you then have a Release you can go back to (or fix forward, discussed in a while) that will reset the change. This principle aligns to the principle from DevOps Handbook of "release new code/features into Production before activating them"
3. **When approaching your change, ALWAYS develop additional roll back steps into a script and test the scripts** - where you really can't script it, you can always fall back to the old way and have a well documented step-by-step approach. Document this in Markdown and check it in with your Source Control so everything lives together in the repo.

With this approach, you can regress as in this scenario:

1. v1.0 is the currently deployed version of the configuration in Production, everything is working fine
2. You want to change an SMB Security Registry Key to a value of "1". It's current value is the Default value, "0".
3. Add an additional configuration item to your DSC Configuration that will set the Registry Key to "0" (it's current value). Write any accompanying Tests that are needed
4. Release the configuration (now version 1.1) to your systems. No change is made, everything should work as expected
5. Update your configuration to version v1.2 with the Reg Key value set to "1". Test as usual and Release this change. Everything looks ok.
6. Realize there is now a problem in Production with SMB due to some issue not seen in test and realize you need to regress
7. Update the Configuration to v1.3 with the Registry Key set back to "0" and release
8. You are now back to the stable state

By following these rules, you set yourself up for easier problem resolution by fixing forward and reverting settings. Sure, there will be some bigger issues that need more work but they can be minimized with this approach. Fixing forward is much nicer because it allows you to see a full timeline of the systems development and change history, including what went wrong. This is extremely important for learning!

> **Incidentally, this approach may be considered wrong to others, but as I say this is not a "one-size-fits-all".** Mine works for me but I am interested in feedback on how others would do it in their situation.

### Identify your target system or service

To build a Release Pipeline for Infrastructure you need to choose a system to target. This could be a system or service that is having regular stability issues through uncontrolled change or it could be a stable system which experiences a slow rate of change such as a File Server. It could be a system comprised of multiple components like a 3 tier application or a single technology like Hyper-V or Domain Controllers.

When getting started there is often a reluctance to start with any serious service and instead use a low risk, low impact service which rarely changes. This is a safe approach for sure however it will definitely limit the impact of the benefit of your Release Pipeline and new approach to Infrastructure-as-Code. For my first Infrastructure-as-Code project I suggested to the organization that I was working with to target their Hyper-V & VMM infrastructure. My thinking was as follows:

* **This was a system that was suffering stability issues** - one of the benefits of Infrastructure-as-Code that I wanted to prove was that it increases stability. I wasn't going to do this on an already stable system.
* **Because of the stability issues, there was a lot of attention on the system** - this change was supposed to be transformational so I needed it to be very visible.
* **Improving the stability of a system such as this would bring big benefits** - it was running the Line of Business Application workloads and had customers who depended on it.
* **Because this was running LOB workloads, any failure introduced by our new method would be business impacting** - this forced us to be very diligent in building a release process that worked hard to reduce the risk of downtime during platform change

This did bring some drawbacks of course:

* Reluctance of stakeholders to accept new methods
* Much longer time to get the system productionized due to the requirement to build a robust release system
* More stakeholders to engage

Overall, I am very happy with the system we chose, because the learning that came with it and the pressure that forced us to learn would not have come with anything lower risk. While I don't suggest that you take crazy risks with your company's systems, find a balance between what you believe you have the ability to do and something that will show real benefit. You will know what is right for you and for your organization.

### Define your goal that will drive development effort

Having a clear goal defined that illustrates your intentions can help to guide conversations, guide decision making and to unite stakeholders to a common purpose. When you're starting on an Infrastructure-as-Code journey, a number of stakeholders can become skeptical. You're proposing a change to the way things are done but not only that, you're proposing automating a lot of the checks and balances that are in place. Those are likely there because of past failures. Failed changes and failed automation that has caused system downtime, weekend overtime and difficult follow up conversations.

Why would this effort be any different?

Having a clearly defined goal helps with alleviating this fear. For example, for my Virtualization-as-Code project at an FSI our goal was clear and simple:

> _"Our goal is to eliminate unplanned downtime of the Virtualization platform as a result of operational change. The Virtualization platform will be a stable, reliable and trusted platform for application teams to deliver on new systems and new ideas, fast."_

By defining this goal and including it inside of the code repository, on the Wiki pages and within update emails every week, we were continually reiterating that we are not trying to do anything that is going to cause impact to the system we are managing. To the contrary, we are striving to be better and this goal would be associated with any stakeholder who associated with us. With every feature, enhancement or change to the code that was happening, the person delivering that should be able to articulate how it supported that goal. While planning our work, anything that did not support that goal did not make it onto the work board.

It also helped us to define our Release Process and what we would expect to be inside of it, because the statement _" … eliminate unplanned downtime of the Virtualization platform as a result of operational change …"_ immediately raised the questions:

1. What might we do or what might we encounter during a Release that would cause downtime?
2. How do we mitigate against that?

This came into play in later stages.

### Map your existing Change process into a Release process

We are trying to automate the process of releasing changes into production and validating them for success. So before introducing anything "new and scary", it makes sense to build a first iteration of a Release process based on what you currently do.

You can list out the tasks you have to do today, along with how you are planning to tackle it with automation.

For the purposes of this we will be covering Change Implementation, which means we still need to go through the process of raising a Change. This includes:

* Implementation Plan
* Test and Validation Plan
* Roll-Back plan

We do of course then need to represent the change at the Change Approval Board (CAB), probably multiple times, because we first need to raise a change to implement into one environment, then another change for the second environment, and repeat until we reach Production.

An example table of activities for Change Implementation is as follows:

| Step | Activity | Detail | Stakeholder | Possible Automation |
|----|----|----|----|----|
| 1 | Check that the Change is Approved | Log into the Change System and validate that the change has been approved and the change window is open. | Change Managers <br>Service Managers <br> Governance Team <br>Operations Team | PowerShell Script <br>OR <br>Runbook |
| 2 | Request permissions from the Privilege Access System | Log into Microsoft Identity Manager (MIM) and request access to the target system. | Security Team <br>Operations Team | PowerShell Script <br>OR <br>Runbook |
| 3 | Implement the change | May be manual via RDP or by running Scripts or PowerShell commands | Service Managers <br>Operations Team | PowerShell Scripts |
| 4 | Validate the change | Take screenshots <br> Collect logs <br> Follow a test plan and save any output | Service Managers <br>Governance Team <br>Security Team <br>Operations Team <br>Change Managers | PowerShell Scripts |
| 5 | Attach evidence to the change and close. | Log into the change system <br>Upload any gathered evidence <br>Close the change <br>Send status notifications  | Service Managers <br>Governance Team <br>Security Team <br>Operations Team <br>Change Managers | PowerShell Script <br>OR <br>Runbook |

With the above listed, we now have a starting point to begin writing a Release Process.

Something else we need to keep in mind is the raising of the change itself. We still need to automate this but we will integrate that into a Build process at a later stage, for now we want to focus on Release automation. Essentially, we are starting at the end and working backwards.

### Identify your stakeholders and build relationships

Pending - This section addresses finding friendlies in an inclusive way

### Start building your Proof of Concept

#### Set up your Release Pipeline Framework

If you don't already have a release pipeline system in place, you can start with this:

TO DO

* Start with Michael Greene's demo CI project
* Replace with the Scripts from this Repo [Pending]

The goal of this step is to establish a build system that can take from your Source Control and run a Build Process and a Release Process, along with Tests. Once we have this in place we can start maturing that Release Process by building the relevant Release Steps.

#### Build the Release Steps

This step is to create the automation for the change implementation process. Each step in the table compiled under _"Map your existing change implementation process"_ has some possible automation ideas. There are 2 ways I usually think about doing these:

1. Write every step in the process in the Release script that will be run by the Build agent
2. Create Orchestrator or Service Management Automation/Azure Automation runbooks to run each stage

My preference is actually for runbooks wherever possible. This is because:

* It allows the manual process steps to start being replaced by people triggering the runbooks, starting with a gradual change to automation
* The Release process can then start calling the same runbooks instead of introducing new code, which is generally considered lower risk
* When manual intervention is required the steps can be run in the same way as the automated release process would do it
* Modular automation is easier to debug and change
* These steps are generally used across every system change and can be leveraged easily at a later stage for a consistent approach
* Runbooks can also be exported and saved into Source Control, or in the case of SMA/AA they can be generated entirely in code and have a Release Pipeline process of their own!

**Complete any Runbook creation before starting with the Release Script so that you can call those Runbooks.**

#### Establish a simple Dev & Release "Sprint" schedule

Pending - This section discusses a first iteration of a release schedule, put in place simply for predictability and planning, it can be used as a starting point for full Agile/Scrum based development later but the point is to give predictability to work.

### Ensure you have Stakeholder Engagement

Pending - This section addresses making sure you have regular communication with stakeholders, some who may want to bury their head and not support you.

### Create User Stories of the Release Process and ensure you have your Minimal Viable Product (MVP)

Pending - This section addresses how you should not get too focused on the release process itself but find a balance between delivering what the stakeholders want and delivering the capability of your solution.

Below shows an example of the MVP release process that was needed for a Virtualization-as-Code project.

> NOTE: In this process it was not required to check the change system automatically or check out permissions from the Privilege Access Management system.

![Example MVP Release Process](/img/example-rp-mvp.JPG)

## Phase 2: Build a Track Record

Pending - This section addresses how you start building trust in the running of this process.

### Iterate on your Process

Pending - This section addresses how you start running your process as a regular activity.

### Make your process visible

Pending - This section addresses how you give stakeholders a view into what's going on.

### Start building your capability

Pending - This section addresses building out your DSC Configurations.

## Phase 3: Use your track record to drive change

Pending

### Identify your desired improvements and build your case

Pending

# Finishing up

Text