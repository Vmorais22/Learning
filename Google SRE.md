# SRE - Site Reliability Engineering 

## Introduction

Historcyally there's this sysadmin model of service managmetn in which we have sysadmins as the persons in charge of running complex services and responding to events and updates. So we distinuigh between the development team and the operations or ops team.
This way of doing things has several advantages, mainly easy to implement. However, the split between developers and ops implies direct and indirect costs.

- Direct costs: Ops team is expensive. It increases as the service and its traffic grows.
- Indirect costs: The split between two groups in background knowledge, different assumptions, risks analysis, vocabulary, communication, goals... They have different objectives.

The idea of SRE team is that software enginers are in charge of running the products. SRE is what happens when you ask a software engineer to design an operations team. The idea is to end up with a team of people who:
- Will quickly become bored of performing tasks by hand.
- Have the skill set to write softrwate to replace their previously manual work.

  
SRE teams focus on egineering to automate tasks and aovid linear scaling of operations with service sieze. 50% of its loadwork is "ops" to ensure have sufficient time to make the service stable and operable.

## DevOps or SRE ?

Althoguh its not the same, DevOps can be think as a generalitzacion of several core SRE principles. Or, with other words, SRE is a specific implementation of DevOps.

## Principles of SRE

In general, an SRE team is responsible for the availability, latency, performance, efficiency, change management, monitoring, emergency response, and capacity planning of their service(s).

### Ensuring a durable focus on enginneering

The operational work for SREs team is 50%. The remaining time is dedicated on coding. The operational work is monitorized and the extra is redirected to product development team. SRE aim for a maximum of two events per on call shift.

### Pursuing Maximum Change Velocity Without Violating a Service’s SLO

The main conflict between developers and ops is the conflict between the innovation speed vs the product stability. We introduce the **error budget** in order to solve it:

100% is not an appropiate reliability target for a system. The user does not see any difference between 99,5 and 100%. Even having in the middle other factos that can interfere such as their latop, wifi connection, etc... So, which is the correct reliability target? This is a product question and it's answer is based on different other questions. The business or product should establish the system's availabity target. Then, **the error budget = 1 - availabilty target**. A 95% available service has a error budget of the 0,05%. We can spend this error budget in whatever we want. We decide where to take the risk and we can mak faster launches.

### Montioring
Monitoring should not require human interpretation; alerts, tickets, and logging serve distinct purposes. Effective monitoring avoids relying on human interpretation and ensures automated response to critical conditions.

- **Alerts**: Signify that a human needs to take action immediately in response to something that is either happening or about to happen, in order to improve the situation.
- **Tickets**: Signify that a human needs to take action, but not immediately. The system cannot automatically handle the situation, but if a human takes action in a few days, no damage will result.
- **Logging**: No one needs to look at this information, but it is recorded for diagnostic or forensic purposes. The expectation is that no one reads logs unless something else prompts them to do so.

### Emergency Response

Realibility is a function of mean time to failure (MTTF) and mean time to repair MTTTR).
On-call playbooks and preparation, including exercises like the "Wheel of Misfortune," improve MTTR during emergencies.

### Change Management

About 70% of outages result from changes in a live system. Best practices involve automation for progressive rollouts, quick problem detection, and safe change rollbacks.

### Demand Forecasting and Capacity Planning

Ensuring sufficient capacity and redundancy for future demand requires accurate organic and inorganic demand forecasts. SREs must be in charge of capacity planning and provisioning, considering both organic and inorganic growth.

### Provisioning:

Provisioning, involving change management and capacity planning, must be conducted quickly, correctly, and only when necessary. Adding new capacity is riskier than load shifting and requires extra caution.

### Efficiency and Performance

Utilization, a function of demand, capacity, and software efficiency, is a key lever for controlling service costs. SREs focus on optimizing provisioning strategy, monitoring performance, and modifying services for improved efficiency.

## Foundations

The basic foundations of SRE include SLOs, monitoring, alerting, toil reduction, and simplicity.

### Implementing SLOs

Service level objectives (SLOs) specify a target level for the reliability of your service. Striking the right balance between investing in functionality that will win new customers or retain current ones, versus investing in the reliability and scalability that will keep those customers happy, is difficult. SLOs are a tool to help determine what engineering work to prioritize. or example, consider the engineering tradeoffs for two reliability projects: automating rollbacks and moving to a replicated data store. By calculating the estimated impact on our error budget, we can determine which project is most beneficial to our users. 

In order to adopt an error budget-based approach to Site Reliability Engineering, you need to reach a state where the following hold true:

- There are SLOs that all stakeholders in the organization have approved as being fit for the product.
- The people responsible for ensuring that the service meets its SLO have agreed that it is possible to meet this SLO under normal circumstances.
- The organization has committed to using the error budget for decision making and prioritizing. This commitment is formalized in an error budget policy.
- There is a process in place for refining the SLO.

### Reliability Targets and Error Budgets

An SLO sets a target level of reliability for the service’s customers. Above this threshold, almost all users should be happy with your service. Below this threshold, users are likely to start complaining or to stop using the service. Once you have an SLO target below 100%, it needs to be owned by someone in the organization who is empowered to make tradeoffs between feature velocity and reliability. This is normally the product owner (or product manager).

But, how to mesure the right number? **Using SLI**. An SLI is an indicator of the level of service that you are providing. Is recommended to think the SLI as a ratio of two numbers. The **number of good events / total number of events**. (for example: Succesful HTTP requests / total HTTP requests). This scalated format helps defining the error budget:

*For example if our SLO is just based on the HTTP request SLI mentioned above and it's 90% it means that our SLO is 90 out of 100 HTTP request should be succesful or, in other words, a budget error of 10 out of 100 HTTP request that will fail. If we have 50 out of 100 HTTP requests failing we are excedding our error budget.*

Also helps on writting alerting logic.

### Specificating SLI

When attempting to formulate SLIs for the first time, you might find it useful to further divide SLIs into:

- **SLI specification:** The assessment of service outcome that you think matters to users, independent of how it is measured.
    - For example ratio of home page requests that loaded in less than 100 ms.
- **SLI implementation:** The SLI specification and a way to measure it.
    - logs, timers, telemetrics....

A single SLI specification might have multiple SLI implementations, each with its own set of pros and cons in terms of quality (how accurately they capture the experience of a customer), coverage (how well they capture the experience of all customers), and cost. To create your first set of SLOs, you need to decide upon a few key SLI specifications that matter to your service. Availability and latency SLOs are pretty common; freshness, durability, correctness, quality, and coverage SLOs also have their place

Draw a high-level architecture diagram of your system; show the key components, the request flow, the data flow, and the critical dependencies. Group these components into categories. Some suggested categories are:

- Request-driven: The user creates some type of event and expects a response.
- Pipeline: A system that takes records as input, mutates them, and places the output somewhere else.
- Storage: A system that accepts data (e.g., bytes, records, files, videos) and makes it available to be retrieved at a later date.

For example:
![image](https://github.com/Vmorais22/Learning/assets/45717130/e0bf58e0-e215-411c-a714-5f8bae6ae927)


### Moving from SLI Specification to SLI Implementation









# The Art of SLOs (by the Google's Customer Reliability Engineering team)

The goal of this section is to show the way Google measures service reliability in terms of:
- **Service Level Indicators (SLI)**: how relaiable our service is
- **Service Level Objectives (SLO)**: descrbies when a service is reliable enought to meet the user needs and happyness.
- **Error budget**: complement of SLO, the accepted level of unreliability.

In other words, providing an objective way of mesuring the reliability.
  
