# SRE Cheat Sheet

## Principles
1. Reliability is the most important feature
2. Users, not monitoring decide reliability
3. Well-engineered (can go only to 3 9s)
    - software = 99.9%
    - operations = 99.99%
    - business = 99.999%
 
### Reliability
Reliability is a measure of how well the service lives up to its expectations.

Principles of reliability
  - What to promise and to whom?
  - What metrics to measure?
  - How much reliability is good enough?
  
In real world, Reliability of a service is quantified by measuring few parmeters which are called `SLI`

#### NOTE: 
1. 100% Reliability is a wrong target. If you are running your service more reliably than you need to, you may be slowing down development
2. Its more expensive to make already reliable services more reliable. At some point the incremental cost of making a service reliable increases exponentially
3. Have ambitions but achievable targets based on how it performs and agreed by all stakeholders
4. Taking reliaility to extreme measures is unproductiove and costly. It should be "Reliable Enough"

#### How to make services reliable?
##### Rolling out changes gradually
  - Deployment with incremental changes
  - Feature toggles
  - Canary deployments with easy rollback that affect only a smaller percent of users initially
##### Remove single point of failure
  - Multi AZ deployments
  - Set up DR in a geographically isolated region
##### Reduce TTD (Time-To-Detect)
  - Catch issues faster by automated alert and monitoring 
  - Monitor SLO compliance and error budget burnout
##### Reduce TTR (Time-To-Resolution)
  - Fix outgages quicker
  - Knowledge sharing via playbooks
  - Automating outage mitigation steps, such as draining from one region to another.
##### Increase TTF / TBF - Expected frequency of failure to 
  - Make services Fault tolerant by running them on multiple AZs
  - Automate manual mitigation steps
##### Inprove Operation Efficiency
  - Post mortems of outages
  - Standardized Infrastructure 
  - Collect data on poor reliability regions and make extra effort to make those reliable
 ```
 |------------|---------------|
 Issue       TTD             TTR
        Time-To-Detect   Time-To-Resolution
 ```
 Expected impact of a failure on error budget over a period of time
 ```
 E ~ (TTR+TTD) * impact % / TTF
 ```

### Measuring Reliability
How is reliability measured?
#### SLI
An SLI is a service level indicator — a carefully defined quantitative measure of user experience / reliability of service.
Simply,
```
SLI = good events / valid events
```
##### Characteristics of good SLI
- Must have a predictable liner realationship with user happiness (Less variance) 
![alt text](https://github.com/anshudutta/sre-cheat-sheet/blob/master/SLI-Metric.png)
- Shows service is working as users expect it to
- Aggregated over a long time horizon

##### Ways of measuring SLI
- Requst Logs
- Exporting metrics
- Front-end load balancer metrics
- Synthetic clients
- Client side instrumentation

##### Types of SLI
- Request Latency
- Error Rate = (500 responses/total requestes) per second
- Time between Failures - Frequency of error occurring over a period of time
- Availability = uptime / (uptime + downtime)
- Durability (Data will be retained over a period of time, measure of data loss)

##### Examples
###### Request-Response systems
 - Availability - Proportion of valid requests served successfully
 - Latency - Proportion of valid requests served faster than a threshold
 - Quality - Proportion of valid requests served without degrading quality
###### Data-Processing Systems
 - Freshness - Proportion of valid data updated more recently than a threshold
                 i.e. is it serving stale data
                 e.g. For a batch processing system its the time since last successful run
 - Correctness - Porportion of valid data producing correct output
 - Coverage - Proprotion of valid data processed successfully
 - Thorughput - Proportion of time where the data processing rate is faster than threshold

##### Managing complex systems
- Start by thinking about user journeys
- SLI and metrics are different. SLI ---> Something is broken, Metric ---> What is broken
- Keep the number of SLIs down to 1-3 per user journey
- Not all metrics make good SLI
- Higher number of SLIs increase load on operations
- Higher signal to noise ratio as they tend to give conflicting signals
- Aggregate similar user journeys to keep the number of SLIs down

#### SLO
An SLO is a service level objective: a target value or range of values for a service level that is measured by an SLI.
They are a fundamental tool in helping your organization strike a good balance between releasing new features and staying reliable for your users. 
They also help your teams communicate on the expectations of a service through objective data.

##### Questions that SLO help answer:
- If reliability is a feature, when do you prioritize it versus other features?
- How fast is too tast for rolling out features?
- What is the right level of reliability for your system?
  
A service level objective usually defines a target level for SLI so that one can continue to provide a reliable service
```
lower bound ≤ SLI ≤ upper bound --> for a defined period of time
```
##### An SLO
- Should be stronger than your SLA to catch issues before they violate customer expectations
- Is an internal promises to meet customer expectations

##### NOTE:
1. Defining `SLO` is an iterative process, needs to be reviewd periodically based on changing business needs and customers
```
|-----------|-----------------|
Initial   Follow up          Periodic
```
2. Edge Cases
Not everything is linear, there are many edge cases in different organizations that don't conform to a single SLO for everything.
###### Example
1. Companies might shift from 3 9s to 4 9s during black friday shopping frenzy to cater to high demands
2. Outage duration can impact customer happiness. The follwing may affect different customers differently
 - A single 4 hour outage
 - Four 1 hour outages
 - Constant rate of 0.5% errors
3. Not all users care about latency the same way. Bots may require lower latency than humans. So its resonable to have different SLOs based on different users

SLO Targets
1. Just high enough to keep customers happy
2. Ambitious but achievable
 
##### The happiness Test
The test states that services need target SLOs that capture the performance and availability levels that if barely met would keep a typical customer happy. 
Simply put, if your service is performing exactly at its target SLOs, your average user would be happy with that performance. 
If it were any less reliable, you'd no longer be meeting their expectations and they would become unhappy. 

If your service meets target SLO, that means you have happy customers. If it misses the target SLO, that means you have sad customers. 

#### SLA
Any company providing a service need to have Service Level Agreements, or SLAs. These are your agreements that you make with your customers about the reliability of your service. An SLA has to have consequences if it's violated, otherwise there's no point in making one. If your customers are paying for something and you violate an SLA, there needs to be consequences, such as giving your customers partial refunds or extra service credits.

if you are only alerted of issues after they violated your SLA, that could be a very costly service to run. Therefore, it is in your best interest to catch an issue before it breaches your SLA so that you have time to fix it. These thresholds are your SLOs, service level objectives. They should always be stronger than your SLAs because customers are usually impacted before the SLA is actually breached. And violating SLAs requires costly compensation. 

```
|---------------|--------|-----------
            :) SLO  :(  SLI  Penalty
```
An SLA
- Must have consequences if violated
- Is an agreement with your customer about reliability of your service

### Error budget
Error budget is a measure of service unreliability that is allowed without breaking SLOs or how much down time is allowed before you have unhappy customers
Error budget hepls you figure out how room we have for mistakes that can make service unreliable.

Error budgets are the tool SRE uses to balance service reliability with the pace of innovation. Changes are a major source of instability, representing roughly 70% of our outages, and development work for features competes with development work for stability. 
The error budget forms a control mechanism for diverting attention to stability as needed.

```
Error Budget = 1 - SLO
```
#### Illustration
---
28 day error budget
- 99.9% = 40 min (Enough time for humans to react)
- 99.99% = 4 minutes 
(Incident responses have to be automated. 
Else make sure change propagates gradually so not all parts of system 
are exposed to change at once giving time for human intervention)
- 99.999% = 24 seconds (Restricting the rate of change so that only 1% of system changes at a given point in time)

Example
---
A 99.9% SLO service has a 0.1% error budget.

If the service receives 1,000,000 requests in four weeks, a 99.9% availability SLO gives us a budget of 1,000 errors over that period.
```
Downtime = 0.001*28*24*60 minutes = 40.32 minutes
```
This is just about enough time for 
- your monitoring systems to surface an issue, 
- a human to investigate and fix it. 

And that only allows for one incident per month

This unavailability can be generated as a result of bad pushes by the product teams, planned maintenance, hardware failures,etc.

#### Benefits
- Common incentives for Devs and ARE
- Dev team can self manage risk
- Unrealistic goals become unattractive

If Servive < Error Budget
 - Dev can push changes more frequently
 - SRE can proactively work on increasing reliability
 
If Service > Error Budget
 - Changes need to be stopped till the system is stable
 
One simple approach is to keep releasing features till error budget is exhausted, then focussing development on reliability improvements untill the budget is refilled
