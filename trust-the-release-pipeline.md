# Overview

**Please Note: This paper is unpolished and at times opinionated and rant-y, it is my expectation to mold this into something polished and presentable based on questions and feedback received so please, do send that and offer your perspectives.**

This paper aims to provide some real world and practical context to implementing a Release Pipeline for Infrastructure. A Release Pipeline is a DevOps process traditionally used for deploying software but is equally applicable to infrastructure when managing Infrastructure-as-Code.

It does not focus on the technical steps, instead, on the process, challenges and high level thinking required to influence this change within an Enterprise organization. 

What is outlined in this paper is a suggested approach for how to adopt a Release Pipeline for infrastructure within an Enterprise, ITIL environment. The approach aligns to what I have actually done in Enterprise customers with Microsoft and what I have learned during those engagements.

_**That said, this should not be treated as an official Microsoft document or any official guidance from Microsoft.**_ This is purely reflecting my own experience, thinking, approach and opinions. Official Microsoft documentation, guidance and assistance may be available.

It is important to note that the Release Pipeline is a concept. The implementation of it can differ dramatically depending on the Team implementing it, the people involved, the requirements of the organization and the operating environment and culture of that organization. Therefore, what is detailed in this document will not be agreed with by everybody and will not make sense in every environment. The intention is to provide you with at least a starting point or a different perspective to help you along your own journey.

# Why am I writing this Paper?
I am writing this paper because I spent a long time on my Infrastructure-as-Code journey doing the wrong thing. Like most technical folks, my view of Infrastructure-as-Code on the Microsoft stack was doing Desired State Configuration. While this is a component of the Infrastructure-as-Code approach, it is by no means the destination. In fact, I came to learn it is the least important element. What I learned to be most important was the delivery mechanism, which I gave little thought to until I had DSC doing incredible things.

I liken this to having spent time building up the pizza-making capability of a delivery service only to be making incredible pizzas that no one was eating - because I couldn't deliver them! Instead, I wish I spent my time building up capability to deliver a plain cheese pizza in 20 minutes, consistently, to as many people who ordered, before I built out my menu.

What I found to be the most difficult is to get an infrastructure-as-code approach accepted by the Enterprise., because this was a change to the norm, this was automation that people had little reason to trust. What ensued was months of building release automation, workflows, feedback loops and so forth that, if we focused on at the start would have made ramping up the DSC code easier.

People often asked me "can DSC do … x…" or "can DSC do … y…". Here's the thing. Yes. DSC can do anything you want it to. DSC is code, code you can write yourself. There may not be Resources readily available but DSC can do anything you want. If you find yourself asking or being asked this question, rather than trying to prove what it can or cannot do, stop yourself. Focus on "how do I deliver my DSC code" and you'll find that is time best spent. Once you break down the barriers to using it in your enterprise, you can start to flow fast.

It is my hope, that this paper, helps you break down those barriers.

# What is the Release Pipeline and what does it mean for Infrastructure
There is an excellent whitepaper written by Michael Greene and Steven Murawski on the Release Pipeline for Infrastructure. It is highly recommended reading [Add Link]. In short, the Release Pipeline is a methodology for how we can take code and deliver it into a production scenario where it can be used, through an automated Testing and Deployment solution.
Release Pipelines can essentially be viewed as automated quality control for code.
When we think about code from an Infrastructure perspective, these can be scripts, modules, runbooks, Desired State Configurations and the like. The code you deliver may just be code deployed as Runbooks for others to consume, or it may be the execution of DSC Configurations themselves to affect change on Production Servers.
Release Pipelines are typically used for software and PaaS/DevOps projects to deliver changes to code into working software, but from an Infrastructure perspective either as part of a DevOps process with an application or purely to manage infrastructure they have immense value.
By implementing a Release Pipeline for Infrastructure and bringing an existing service under code control, you can effectively eliminate manual changes. It is perfectly acceptable to think you can start documenting the configuration of your service in Desired State Configuration code with Environment Files. That you can release the same configurations to different environments and get the same results. That you can make changes to environments by making a change to your configurations. And, that you can test the outcome of these changes as they are rolled out into your environments and get real time feedback on success or failures so you can take action. It is completely reasonable to think you can have your whole infrastructure service completely captured in code that you can deploy or redeploy anywhere are any time.

And that, is the true definition of Agility. I have been around a number of customers who I listen talk about being Agile and they refer to being able to iterate using an Agile process. That, to me, is not being Agile. For me, true Agility comes from being able to pivot quickly and respond to new demands quickly with high quality and stability. To be in one cloud service today and be able to, at short notice, re-deploy your service into a new location or environment and to swing your users to that new environment seamlessly. To be able to hear about a security threat one morning and be ready to deploy mitigating changes by lunch without breaking a sweat.

That, to me, is true agility and that is completely reasonable to expect. It all relies on moving to a Release Pipeline model for infrastructure and that can be a challenge when it means affecting change within your organization.

# What is the challenge to adoption
The challenge to adoption is generally down to a few key things:
1. A gap in education and understanding. In my experience, I have seen technology teams working to understand this process, but the persons involved in the delivery chain for production changes are not. The technology teams then come up against the task of having to educate those other teams before they can adopt these new practices
2. A lack of Trust. The other stakeholders involved in the delivery process coming from an ITIL world typically have a distrust for each other. This comes from years of implementing mitigations and multiple processes following the outcome of failed change in the environment. It also comes from the Silo approach that a lot of organizations take. There is a funny image used to represent how Microsoft used to be with each team pointing weapons at each other. In fact, this is still a common thing among enterprise IT Teams.
3. Reluctance to Change. Change is hard, it usually brings instability, instability brings risk and ITIL organizations have spent a long time building complex processes that remove risk - sometimes at the cost of preventing change. When a gap in education about where you're going exists, and you have a lack of trust for the other teams involved, the suggestion of bringing potential instability and situations where you have to learn and trust each other into daily operations seems counter to what the organization has spent so long building.

When a technology operations team or engineering team is trying to change the way they manage and deliver infrastructure, these 3 things act as a barrier to getting over the line.

# Where does this typically apply?
You may be reading this and thinking that this doesn’t represent your scenario at all. That is awesome! You work in a much healthier organization than a number of other people, but hopefully there is still some elements of this paper which will be useful to you. The perspective this paper is written from is the following:

* Working in very large enterprises that are very ITIL or legacy driven
* Company cultures where Trust between different teams in the IT organization is eroded
* Companies that employ people who they do not necessarily invest in keeping their skills up to date or who don't necessarily invest in taking that initiative themselves
* The IT organization is largely resistant to change external to their own control
* Organizations that have built up drawn out change processes that may include:
    * Releasing a change across multiple environments with breaks in between
    * Change process requires you to state the change, how, rollback, success measurements
    * Security team have concerns/reservations about automation

If this describes you, then typically it is safe to assume you've hit a brick wall.

# What this paper assumes about you
If the previous section sounds like your scenario and you've run up against a bit of a wall with operationalizing DSC. Or, you might be only just starting off on your journey and wondering how to begin. Either way, the assumption is that you're either learning or already have:

* Built DSC configurations and separated Environment Data
* Using Team Foundation Server/VSTS
* Using Psake or InvokeBuild

Any code samples provided to support this paper will plug into these systems. If you're doing something different, that’s more than fine, this is about the process rather than the technology, there is just some adaptation you will need to do yourself.

# How do we integrate a Release Pipeline and use Infrastructure-as-Code?
There is no one-size-fits all and every approach is going to be different, because every organization and every team is different. The approach I learned based on environments like the above was:

* Educate the stakeholders by Integrating into the current process. Prove the concept.
* Build Trust in the process by showing results and success
* Use your track record of proven methodology to show you can
    * Reduce the time span of which you deploy changes to the environment with low risk
    * Make infrastructure changes during business hours
    * Bring consistency to the Dev, Test, UAT and Production environments

By following these three phases and taking stakeholders on the journey with you, it generally leads to a more successful approach. This means focusing on the Release process first and less on the DSC side until a little later. Remember, DSC is not a solution in itself, it is a component of the overall solution of Infrastructure-as-Code. The primary component of which, is the Release Pipeline.

# The 3 steps of the Process
## Prove the Concept

### Start with your PowerShell Code
Pending

### Understand and demonstrate how you would deal with "Roll Back"
Pending

### Identify your target system or service
Pending

### Define your goal that will drive development effort
Pending

### Map your existing Change process into a Release process
Pending

### Identify your stakeholders and build relationships
Pending

### Start your Proof of Concept
Pending

### Ensure you have Stakeholder Engagement
Pending

### Create User Stories of the Release Process and ensure you have your Minimal Viable Product (MVP)
Pending

## Build a Track Record
Pending

### Iterate on your Process
Pending

### Make your process visible
Pending

### Start building your capability
Pending

## Use your track record to drive change
Pending

### Identify your desired improvements and build your case
Pending

# Finishing up
Text