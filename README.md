# SRE Cheat Sheet

## Principles
1. Reliability is the most important feature
2. Users, not monitoring decide reliability
3. Well-engineered (can go only to 3 9s)
 - software = 99.9%
 - operations = 99.99%
 - business = 99.999%
 
Note: Taking reliaility to extreme measures is unproductiove and costly. It should be "Reliable Enough"

### Error budget
Error budgets are the tool SRE uses to balance service reliability with the pace of innovation. Changes are a major source of instability, 
representing roughly 70% of our outages, and development work for features competes with development work for stability. 
The error budget forms a control mechanism for diverting attention to stability as needed.

An error budget is 1 minus the SLO of the service. A 99.9% SLO service has a 0.1% error budget.

If our service receives 1,000,000 requests in four weeks, a 99.9% availability SLO gives us a budget of 1,000 errors over that period.

#### Example
---
28 day error budget
- 99.9% = 40 min (Enough time for humans to react)
- 99.99% = 4 minutes 
(Incident responses have to be automated. 
Else make sure change propagates gradually so not all parts of system 
are exposed to change at once giving time for human intervention)
- 99.999% = 24 seconds (Restricting the rate of change so that only 1% of system changes at a given point in time)

### Reliability
Reliability is a measure of how well the service lives up to its expectations.

Principles of reliability
  - What to promise and to whom?
  - What metrics to measure?
  - How much reliability is good enough?
  
In real world, Reliability of a service is quantified by measuring few parmeters which are called `SLI`

NOTE: 
1. 100% Reliability is a wrong target. If you are running your service more reliably than you need to, you may be slowing down development
2. Its more expensive to make already reliable services more reliable. At some point the incremental cost of making a service reliable increases exponentially
3. Have ambitions but achievable targets based on how it performs and agreed by all stakeholders

### Measuring Reliability
How is reliability measured?
### SLI
An SLI is a service level indicator — a carefully defined quantitative measure of some aspect of the level of service that is provided.
Simply,
```
SLI = good events / valid events
```
- Request Latency
- Error Rate = (500 responses/total requestes) per second
- Availability = uptime / (uptime + downtime)
- Durability (Data will be retained over a period of time, measure of data loss)

---
Examples

- User-facing serving systems - availability, latency, and throughput.
- Storage systems - latency, availability, and durability
- Big data systems - throughput and end-to-end latency

```
Although 100% availability is impossible, near-100% availability is often readily achievable, 
and the industry commonly expresses high-availability values in terms of the number of "nines" 
in the availability percentage. 
For example, 
availabilities of 99% and 99.999% can be referred to as "2 nines" and "5 nines" availability, respectively, 
and the current published target for Google Compute Engine availability is “three and a half nines”—99.95% availability.
```

### SLO
An SLO is a service level objective: a target value or range of values for a service level that is measured by an SLI.
They are a fundamental tool in helping your organization strike a good balance between releasing new features and staying reliable for your users. 
They also help your teams communicate on the expectations of a service through objective data.

Questions that SLO help answer:
- If reliability is a feature, when do you prioritize it versus other features?
- How fast is too tast for rolling out features?
- What is the right level of reliability for your system?
  
A service level objective usually defines a target level for SLI so that one can continue to provide a reliable service
```
lower bound ≤ SLI ≤ upper bound --> for a defined period of time
```

NOTE:
1. Defining `SLO` is an iterative process, needs to be reviewd periodically based on changing business needs and customers
```
|-----------|-----------------|
Initial   Follow up          Periodic
```
2. Edge Cases
Not everything is linear, there are many edge cases in different organizations that don't conform to a single SLO for everything.
Examples
---
1. Companies might shift from 3 9s to 4 9s during black friday shopping frenzy to cater to high demands
2. Outage duration can impact customer happiness. The follwing may affect different customers differently
 - A single 4 hour outage
 - Four 1 hour outages
 - Constant rate of 0.5% errors
3. Not all users care about latency the same way. Bots may require lower latency than humans. So its resonable to have different SLOs based on different users
 
#### The happiness Test
The test states that services need target SLOs that capture the performance and availability levels that if barely met would keep a typical customer happy. 
Simply put, if your service is performing exactly at its target SLOs, your average user would be happy with that performance. 
If it were any less reliable, you'd no longer be meeting their expectations and they would become unhappy. 

If your service meets target SLO, that means you have happy customers. If it misses the target SLO, that means you have sad customers. 


### SLA
Any company providing a service need to have Service Level Agreements, or SLAs. These are your agreements that you make with your customers about the reliability of your service. An SLA has to have consequences if it's violated, otherwise there's no point in making one. If your customers are paying for something and you violate an SLA, there needs to be consequences, such as giving your customers partial refunds or extra service credits.

if you are only alerted of issues after they violated your SLA, that could be a very costly service to run. Therefore, it is in your best interest to catch an issue before it breaches your SLA so that you have time to fix it. These thresholds are your SLOs, service level objectives. They should always be stronger than your SLAs because customers are usually impacted before the SLA is actually breached. And violating SLAs requires costly compensation. 

```
|---------------|--------|-----------
            :) SLO  :(  SLI  Penalty
```


