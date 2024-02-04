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


  
