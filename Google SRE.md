# SRE - Site Reliability Engineering 

1. [ Introduction ](#Introduction)
2. [ DevOps or SRE ](#DevOpsorSRE)
3. [ We also have CRE ](#WealsohaveCRE)
4. [ Principles of SRE ](#PrinciplesSRE)
5. [ Fundations ](#Foundations)
6. [ SLA Service Level Agreements ](#SLA)
7. [ Choosing a good SLI ](#choosingSLI)

<a name="Introduction"></a>
## Introduction

Historcyally there's this sysadmin model of service managmetn in which we have sysadmins as the persons in charge of running complex services and responding to events and updates. So we distinuigh between the development team and the operations or ops team.
This way of doing things has several advantages, mainly easy to implement. However, the split between developers and ops implies direct and indirect costs.

- Direct costs: Ops team is expensive. It increases as the service and its traffic grows.
- Indirect costs: The split between two groups in background knowledge, different assumptions, risks analysis, vocabulary, communication, goals... They have different objectives.

The idea of SRE team is that software enginers are in charge of running the products. SRE is what happens when you ask a software engineer to design an operations team. The idea is to end up with a team of people who:
- Will quickly become bored of performing tasks by hand.
- Have the skill set to write softrwate to replace their previously manual work.

  
SRE teams focus on egineering to automate tasks and aovid linear scaling of operations with service sieze. 50% of its loadwork is "ops" to ensure have sufficient time to make the service stable and operable.

<a name="DevOpsorSRE"></a>
## DevOps or SRE ?

Althoguh its not the same, DevOps can be think as a generalitzacion of several core SRE principles. Or, with other words, SRE is a specific implementation of DevOps.

<a name="WealsohaveCRE"></a>
## We also have CRE

Customer Reliability Enginnering (CRE) focus on breaking down organizatyional barries not between individual silos in a  same company but between the cloud customer and its platform provider. Make sure that everyone has visibility into the system so the customer can see how the platgorm is performing.

- Reliability is the most important feature.
- Users decide reliability, noy monitoring.

<a name="PrinciplesSRE"></a>
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

<a name="Foundations"></a>
## Foundations

The basic foundations of SRE include SLOs, monitoring, alerting, toil reduction, and simplicity.

### Implementing SLOs

Service level objectives (SLOs) specify a target level for the reliability of your service. Striking the right balance between investing in functionality that will win new customers or retain current ones, versus investing in the reliability and scalability that will keep those customers happy, is difficult. SLOs are a tool to help determine what engineering work to prioritize. or example, consider the engineering tradeoffs for two reliability projects: automating rollbacks and moving to a replicated data store. By calculating the estimated impact on our error budget, we can determine which project is most beneficial to our users. 

In order to adopt an error budget-based approach to Site Reliability Engineering, you need to reach a state where the following hold true:

- There are SLOs that all stakeholders in the organization have approved as being fit for the product.
- The people responsible for ensuring that the service meets its SLO have agreed that it is possible to meet this SLO under normal circumstances.
- The organization has committed to using the error budget for decision making and prioritizing. This commitment is formalized in an error budget policy.
- There is a process in place for refining the SLO.

A good SLO should be ambitious and yet achievable.  Plus, you’ll want to find the right point where it’s good enough to keep customers happy.

### Reliability Targets and Error Budgets

An SLO sets a target level of reliability for the service’s customers. Above this threshold, almost all users should be happy with your service. Below this threshold, users are likely to start complaining or to stop using the service. Once you have an SLO target below 100%, it needs to be owned by someone in the organization who is empowered to make tradeoffs between feature velocity and reliability. This is normally the product owner (or product manager).

But, how to mesure the right number? **Using SLI**. An SLI is an indicator of the level of service that you are providing. Is recommended to think the SLI as a ratio of two numbers. The **number of good events / total number of events**. (for example: Succesful HTTP requests / total HTTP requests). This scalated format helps defining the error budget:

*For example if our SLO is just based on the HTTP request SLI mentioned above and it's 90% it means that our SLO is 90 out of 100 HTTP request should be succesful or, in other words, a budget error of 10 out of 100 HTTP request that will fail. If we have 50 out of 100 HTTP requests failing we are excedding our error budget.*

*Another example, if we have a 99.9% of availability that implies a 0.1% of unavailabiltiy. This, in 28 days (one month), in minutes, implies 40.32 minutes. This is the amout of time our service can be down without violating the SLO. Not a lot of time to fix an issue...*

If you ran out of budget, you need to take actions to improve the reliability of your service.

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

For first attempts always choose something that requires a minimum engineering work. Because you need enough information to measure the SLI and this implies making changes on the code. Measure times, count number of events... For example:
- **API and HTTP server availability and latency**: 5XX rsponses count against SLO while all other requests are considered succesfful.. The availability is the propotion of succesful request and the latency the protion of requests faster enough to fit in our defined threshold.
- **Pipeline freshness, coverage, and correctness**: Freshnes can be calculated via saving timestamps and retrieving them. Coverage can be set checking how many records are being processed succesfully, etc.

At the end, we are implementing a white-box monitoring system that collects metricos from various parts of the components. With this metrics we obtain data about how our system works and, choosing an appropiate window time, we can see the percentage of this SLI and determine which should be our SLO. The matter now is, how to choose an appropaite time window? 

- For example don't choose a month if our traffic differs significatly from weekday to weekend. Like our case in Gold team. Use always the same numbers of weekends.
- Shorter time windows allow us to make decisions more quickly. Small course corrections and small waiting time to see the results.
- On the other hand, longer time periods are better for strategic decsions..
- **Four-week rolling window** is a good general-purpose interval.

### Getting Stakeholder Agreement

- The product managers have to agree that this threshold is good enough for users—performance below this value is unacceptably low and worth spending engineering time to fix.
- The product developers need to agree that if the error budget has been exhausted, they will take some steps to reduce risk to users until the service is back in budget (as discussed in Establishing an Error Budget Policy).
- The team responsible for the production environment who are tasked with defending this SLO have agreed that it is defensible without Herculean effort, excessive toil, and burnout—all of which are damaging to the long-term health of the team and service.
- 
 #### Approving the error budget

 Agreeing on the SLO implies approving the error budgte. Getting the error budget by all the stakeholders is important.
 
 - If the SRE feel that the SLO is not defensible we can attempt to relax some objectives.
 - If the development team and product manager feel that the increased resources they’ll have to devote to fixing reliability will cause feature release velocity to fall below acceptable levels, then they can also argue for relaxing objectives.
 - If the product manager feels that the SLO will result in a bad experience for a significant number of users before the error budget policy prompts anyone to address an issue, the SLOs are likely not tight enough.

#### Exhausting the error budget

This policy should cover the specific actions that must be taken when a service has consumed its entire error budget for a given period of time, and specify who will take them.

- The development team gives top priority to bugs relating to reliability issues over the past four weeks.
- To reduce the risk of more outages, a production freeze halts certain changes to the system until there is sufficient error budget to resume changes.
- Another option to reduce risks is to release new functionalities to a reduced set of the customer base to reduce the impact in case of negative experience.

#### Adavnced techinques

We can think of the error budget as an economic budget. We can spend extra budget at the end of the quarter or apply different strategies to woirk with low error budget. Some advanced techniques are:
- Dynamic release cadence based on the the remaining error budget.
- Always store a porpotion of error budget to cover unexpected events (have a tailbone).
- Have alerts based on the error budget (for example alert when we consum more than X % of the error budget during an incidence).
- Apply exceptions to the error budgets when is needed to release criticial new features. This shoudl be agreeded with the stakeholders.

#### Documenting the SLO and Error Budget Policy

An appropriately defined SLO and error Budget should be documented in a prominent location where other teams and stakeholders can review it. This documentation should include the following information:

- SLO
  - Autor, reviewers and approvers. The date.
  - Objectives and the SLI implementations.
  - How the error budget is calculated and consumed.
  - Example: https://sre.google/workbook/slo-document/
- Error Budget
  - Autor, reviewers and approvers. The date.
  - The actions to be taken in response to budget exhaustion.
  - Example: https://sre.google/workbook/error-budget-policy/
 
#### Continuous Improvement of SLO Targets

Before you can improve your SLO targets, you need a source of information about user satisfaction with your service.

You can also check the number of tickets arraised with the budget loss per day.

![image](https://github.com/Vmorais22/Learning/assets/45717130/04d393d6-0911-4e8e-abff-e95a0d54b2bb)

In the example above we see two outliers: one day with only 5 tickets, where we lost 10% of our error budget, and one day with 40 tickets, on which we lost no error budget. Both warrant closer investigation. If some of your outages and ticket spikes are not captured in any SLI or SLO, or if you have SLI dips and SLO misses that don’t map to user-facing issues, this is a strong sign that your SLO lacks coverage.

#### Institute an aspirational SLO

Sometimes you determine that you need a tighter SLO to make your users happy, but improving your product to meet that SLO will take some time. If you implement the tighter SLO, you’ll be permanently out of SLO and subject to your error budget policy. In this situation, you can make the refined SLO an aspirational SLO—measured and tracked alongside your current SLO, but explicitly called out in your error budget policy as not requiring action. This way you can track your progress toward meeting the aspirational SLO, but you won’t be in a perpetual state of emergency.

### Decision Making Using SLOs and Error Budgets

By acknowledging that a specific quantity of unrealiability is acceptable provides a budget for failure that allows us to spent time on developing new features. The rest is dedicated to make the system reliabe enough. The SLO determines how fast you can develop. Under the threshold? You can run. Over the budget? You need to slow down.

You need to know:

- What you are promising and to whom?
- How to measure. Which metrics?
- Which is the accepted realibility.

A good way to know which SLo are the most appropiate to our service is via the **Happiness test**. Is based on the happiness of the clients. Meeting the SLO and the desired reliablity makes clients happy. SLo should be applied to all the customer base.

### Improve reliability
At the end our error budget is related to the Time to Detect (TTD) an error, the Time To Resolve (TTR) this issue, the impact of the issue based on users/functionlaity and the Time To Failure (TTF). 

![image](https://github.com/Vmorais22/Learning/assets/45717130/3e5f2273-d954-4e6d-92d0-49b03181419f)

By solving this factors, we improve the reliability of our service:

- Reducing detection time by improving the mesuring and alerting of incidences.
- Reducing repair time by writting a playbok/runbook to imrpove the solving process.
- Reduce impact by reducing the numbers of users that can be affected (by releasing to a subset of all the users, for example).
- Reduce the failure frequency. Running the service on multiple hosts with automatic redirection... There are a lot's of strategies.

In addition to the topis above that are also helpful to improve realiabiltiy:

- Analyze error budget spent and see if its uneven and detect which are the principal causes of the bigger ratios of budget spent so focus next actions on this parts.
- Standarize infraestructure (software nad hardware).
- Being able to quicky rollback a release and revert changes.
- Report the bug in postmortems (bugs in the product, in the code or even in the SLO iself).
- Gradually changes in productions (release phases, gradually incremental).

<a name="SLA"></a>
## SLA Service Level Agreements

**Agrements with customers about the reliability of your service**. If your customers are paying for a service and we as organmization violate the SLA there needs to be consequences ax extra free service of refunds. We want to catch issues before catching SLA. The difference between SLA and SLO is SLA is a external promise with monetary consequences while SLO is an internal promise to meet customers expectations. SLOs should be stronger than your SLAs to catch issues before they violate customer expectation. For example, if our SLA is that all HTTP requests are returned in 300 ms, our SLO should be to be returned in 200 ms.

<a name="choosingSLI"></a>
## Choosing a good SLI

How to specify meaningful SLI to drive those SLO?

### Metrics and mesurement

Rememeber than SLO should represent the line between happy and unhappy users. We need to find ways to quantify the user experience. We need SLI that has almost a linear relationship with happiness of our users. That we can predict it based on the metric.

System metrics like load average, CPU utilization, memory usage, or bandwidth are commonly graphed and visible on monitoring dashboards. It's tempting to reach for these metrics when looking for SLIs because sharp changes in them are often associated with outages. But that's not a good idea.

A good SLI metric should be:

- As said, predictable relatinoship with user happiness.
- Shows service is working as users expect it to.
- Expressed as; good evenets/valid events.
- Aggregated over a long time horizon. A low level of variance (more stabilized) makes easier to detect changes and define a thereshold.

![image](https://github.com/Vmorais22/Learning/assets/45717130/9a5ccda4-69d5-49c4-bb7a-aabf8e9a4c87) 
![image](https://github.com/Vmorais22/Learning/assets/45717130/2b67165b-87a6-4857-a206-43b780edd5c9)

How to have a good metric then? There are **5 ways to measure any SLI and each has their own set of advantages and disadvantages**:

- Request logs
- Application metrics
- Front-end infraestructure metrics (Cloud provider)
- Synthetic clients - integration tests.
- Client-side instrumentation.

### Commonly used SLI
We usually find that any number of specific root causes collapse down to a small set of observable symptoms. Our SLI menu gives you guidelines for what types of SLI you are likely to want to measure for a given user journey.

![image](https://github.com/Vmorais22/Learning/assets/45717130/9f464f0c-7aaa-4064-9eab-72301e651b15)
- If your service is responding to a user's request, you want to measure how fast those responses are and how many of them are unsuccessful.
  - Availability ->  **The proportion of valid requests served succesfully**. Determine which requests are valid and which responses are          successful.
  - Latency ->  A system is not perceived as interactive by its users if the requests are not responded to in a timely fashion. So we             have here **the proportion of valid requests served faster than a threshold**. Which are valid? Which is a good threshold?
  - Quality -> If your system has mechanisms to trade off quality of the response returned to the user with something else, such as CPU or        memory utilization, you should track this graceful degradation of service with a quality SLI. So **the proportion of valid requests           served without degrading quality**.
- If the user is expecting some data to be processed, then they will probably have expectations that processing completes within a reasonable time frame and processes all the data it should have without errors. Also is important the freshness (the timestamp of the requested data and the most recent data available) and  the correctness (Percentage of responses that contain accurate information or meet predefined criteria).
  - Freshness -> When processing data is common that the output degrades over time as new input data is generated. **The proportion of valid        data updated more recently than a threshold.** Which of the data this system processes are valid? When the timer for measuring the           freshness of the data starts and stops?
  - Correctness -> **The proportion of valid data producing correct output**. Which data is valid? How to determine output is correct? A good exercise is to have golden input with well know output and test the suystem with it.
  - Coverage -> **The proportion of valid data processed succesfully**. Which data is valid? AHow to determine data was succesfully proccessed?
  - Throughput -> **The proportion of time where data processing rate is faster than threshold.** The most common unit is byte/seconds.
- If the user is giving you some of their data to store and want to retrieve that data again, you  want to measure the durability of your storage layer.

As said, SLI must have or should have a rate expression between good events/valid events so it fails between 0 and 100%. A intuitive scale. Secondly,it provides a consistent and exportable format. But, **what's a valid event?** Sometimes you may need to completely exclude some events recorded by your underlying monitoring metrics from being included in your SLI, so they cannot consume your Error Budget. A good event, on the other hand, is determined from what we expect the system to return/response.

### Managing complexity

You should aim just 1-3 SLI per journey. Even with complex services. More SLI results in higher cognitive load and increases the probability of conflicts beteen signals. Not all journeys are important also. We can aggregate similar journeys to just one higher. We should prioritize important journeys.

For example. We can have 4 journeys such as 'open an app', search in a field text, filter the results, click the details of one element. 4 different journeys with its, for example, latency SLI. We can aggregate this in meta-joruney called 'browse' and sum all the valid events and good evenets and have browsing SLI. This have its disadvantatges because you are losing information about specific parts of the flow.

### Setting Reliability targets

User expectations are strongly tied to past performance. If you've based your SLIs on already existing monitoring metrics, you can take a look at this historical data and choose a target that you believe you have a good chance of meeting over the short to medium term based on the past performance of that service. However, here we are assuming that our clients are currently and were in the past happy.

We have maybe a divergence between the aspirational targets that the business needs and the real achievable targets that the past perfomance represents. We need a process to drive convergence. We can always asks users for feedback to see if our users and unhappy despite being on SLO. The continuos improvement should be once a year at least.
# The Art of SLOs (by the Google's Customer Reliability Engineering team)

The goal of this section is to show the way Google measures service reliability in terms of:
- **Service Level Indicators (SLI)**: how relaiable our service is
- **Service Level Objectives (SLO)**: descrbies when a service is reliable enought to meet the user needs and happyness.
- **Error budget**: complement of SLO, the accepted level of unreliability.

In other words, providing an objective way of mesuring the reliability.
  
