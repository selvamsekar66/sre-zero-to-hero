# Error Budget

An error budget is the amount of unreliability a service is allowed to have while still meeting its SLO during a defined measurement period.

> SLO tells us how reliable the service should be.  
> Error budget tells us how much unreliability we can afford.

The relationship is simple:

```text
SLO = reliability target
Error Budget = allowed unreliability
```

---

## 1. What is an error budget?

An error budget is the gap between a target reliability level and 100% reliability.

For example:

```text
SLO = 99.9% availability
Error Budget = 100% - 99.9% = 0.1%
```

This means the service can be unavailable for 0.1% of the time over the agreed measurement window.

---

## 2. Simple example

Suppose the service has an availability SLO of 99.9%.

```text
SLO = 99.9% availability
```

That means:

```text
100% - 99.9% = 0.1%
```

So the service is allowed to be unavailable for 0.1% of the time.

For a 30-day month:

```text
30 days × 24 hours × 60 minutes = 43,200 minutes
43,200 × 0.1% = 43.2 minutes
```

So the service has approximately:

```text
43.2 minutes
```

of allowed downtime during that 30-day period.

---

## 3. Why do we need an error budget?

Without an error budget, teams can get stuck in two extremes.

### Extreme 1: Focus only on reliability

The team may avoid production changes because:

> "Changes can cause incidents."

This can slow down:

- releases
- new features
- architecture improvements
- technology upgrades

### Extreme 2: Focus only on delivery

The team ships features without thinking about reliability.

This can lead to:

- more incidents
- more customer impact
- increased downtime
- higher operational load
- reliability problems accumulating over time

### Error budgets create balance

They give engineering and product teams a measurable way to balance:

```text
Reliability ↔ Innovation
```

The idea is:

- if the service is within its reliability target, the team has room to ship normal changes
- if the service is consuming too much of its budget, reliability work becomes more important

This is a key SRE principle: reliability is treated as a measurable engineering concern, not an unlimited requirement.

---

## 4. Relationship between SLI, SLO, and error budget

These concepts are connected.

```text
SLI
 ↓
What are we measuring?

SLO
 ↓
What reliability target are we setting?

Error Budget
 ↓
How much unreliability can we afford?
```

### Example

Suppose an e-commerce application has:

```text
SLI: successful checkout requests
SLO: 99.9% of checkout requests should succeed
Error Budget: 0.1% of checkout requests can fail
```

- the SLI provides the measurement
- the SLO defines the target
- the error budget represents the allowed unreliability

---

## 5. Error budget calculation

For an availability or success-rate SLO, the basic calculation is:

```text
Error Budget = 100% - SLO
```

### Example

```text
SLO = 99.95%
Error Budget = 100% - 99.95% = 0.05%
```

For a 30-day month:

```text
43,200 total minutes
43,200 × 0.05% = 21.6 minutes
```

So the service is allowed roughly:

```text
21.6 minutes
```

of unavailability.

> This simple formula works well for percentage-based availability or success-rate SLOs.

For other SLOs, the error budget is understood as the amount by which the service can miss its objective.

---

## 6. Common availability examples

| SLO | Error Budget | Approx. downtime / 30 days |
| --- | ---: | ---: |
| 99% | 1% | 7.2 hours |
| 99.9% | 0.1% | 43.2 minutes |
| 99.95% | 0.05% | 21.6 minutes |
| 99.99% | 0.01% | 4.32 minutes |
| 99.999% | 0.001% | 25.9 seconds |

> Do not choose a very high SLO just because it looks impressive.

Higher reliability usually means:

- more infrastructure
- more redundancy
- more engineering effort
- more monitoring and operational complexity
- higher costs

The SLO should match the real user and business requirement.

---

## 7. Error budget is not only about downtime

Error budgets can be tied to many kinds of SLOs.

### Availability

```text
SLO = 99.9% of requests should succeed
Error Budget = 0.1% of requests
```

### Latency

```text
SLO: 99% of requests should complete within 500 ms
```

The remaining 1% represents the amount of requests allowed to miss the latency target.

It does not mean the error budget is simply "1% milliseconds."

### Other examples

Error budgets may be associated with:

- availability
- latency
- correctness
- freshness
- data-processing success
- queue processing
- successful transactions

The key idea is:

> The error budget comes from the SLO and represents the amount by which the service can miss that objective.

---

## 8. The error budget has a measurement window

An error budget always needs a measurement period.

For example:

```text
SLO = 99.9%
Measurement window = 30 days
Error Budget = 43.2 minutes
```

The budget is evaluated over that defined period.

Different services may use different windows, such as:

- 7 days
- 28 days
- 30 days
- 4 weeks
- quarter

The important thing is to define the window clearly.

---

## 9. What happens when we consume the error budget?

Imagine:

```text
Monthly Error Budget = 43.2 minutes
Used = 10 minutes
Remaining = 33.2 minutes
```

The service is still within its SLO budget and can continue normal work while monitoring reliability.

Now imagine:

```text
Monthly Error Budget = 43.2 minutes
Used = 40 minutes
Remaining = 3.2 minutes
```

The team is close to using up the budget and should pay more attention to reliability.

Examples of actions:

- investigate recurring incidents
- fix reliability issues
- improve monitoring
- review risky deployments
- address capacity problems
- improve failure handling
- reduce operational toil

---

## 10. Error budget is a finite budget

Think of the error budget like money.

```text
Monthly Error Budget = 43.2 minutes
```

During the month:

```text
Day 1: 5 minutes consumed
Day 10: 20 minutes consumed
Day 20: 35 minutes consumed
```

Remaining budget:

```text
43.2 - 35 = 8.2 minutes
```

> The budget is finite for the measurement window.

If the service consumes the budget quickly, there is less tolerance for additional reliability problems during the remaining period.

---

## 11. What happens when the error budget is exhausted?

Suppose:

```text
SLO = 99.9%
Error Budget = 43.2 minutes
Actual downtime = 60 minutes
```

The service has exceeded its error budget.

This means:

```text
SLO was not achieved
```

At this point, the organization may prioritize reliability work over feature delivery.

```text
Normal state:
Feature work + reliability work

Budget getting low:
Reliability work becomes higher priority

Budget exhausted:
Reliability restoration becomes the highest priority
```

This is usually defined in an error budget policy.

---

## 12. Error budget policy

An error budget policy defines what the team should do when reliability is getting worse.

It answers:

> What actions should we take when reliability is degrading?

### Budget healthy

```text
A lot of error budget remains

→ normal development
→ normal releases
→ continue monitoring
```

### Budget getting low

```text
Budget is being consumed quickly

→ review risky changes
→ investigate reliability issues
→ increase reliability focus
```

### Budget exhausted

```text
Budget = 0

→ prioritize reliability
→ investigate causes
→ restrict or pause high-risk changes
→ restore the service to the target reliability level
```

> These examples are not universal SRE rules.

Each organization should define its own policy based on:

- service criticality
- business requirements
- risk
- incident severity
- remaining budget
- burn rate
- deployment practices

---

## 13. Error budget and deployments

One of the main uses of an error budget is to guide deployment decisions.

### Service A

```text
SLO = 99.9%
Error budget remaining = 90%
```

The team has plenty of room for normal engineering changes.

### Service B

```text
SLO = 99.9%
Error budget remaining = 2%
```

The service has already experienced significant reliability problems.

A large, risky deployment may not be appropriate.

This creates a useful feedback loop:

```text
Deploy
 ↓
Observe production behavior
 ↓
Measure SLI
 ↓
Compare against SLO
 ↓
Check budget consumption
 ↓
Make release or reliability decision
```

---

## 14. Error budget burn rate

Burn rate tells us how quickly we are consuming the error budget.

### Example

```text
Monthly error budget = 43.2 minutes
```

If the service consumes:

```text
10 minutes in one hour
```

that is a much faster burn rate than expected.

### Why it matters

Two services may consume the same number of minutes, but the timing matters a lot.

```text
Service A: used 10 minutes over 20 days
Service B: used 10 minutes in 1 hour
```

Service B is much more urgent because it is consuming the budget far faster.

---

## 15. Error budget vs burn rate

These are related but different concepts.

### Error budget

Answers:

> How much unreliability can we afford?

### Burn rate

Answers:

> How quickly are we consuming that budget?

Think of it like money:

```text
Error budget = money available
Burn rate = how quickly you are spending it
```

For example:

```text
Monthly budget = ₹10,000
Spend = ₹9,000
```

This tells us the total spent, but not the urgency.

If it was spent over 30 days versus 2 days, the situation is very different.

> A burn rate of 1 means the budget is being consumed at the expected pace.
> A burn rate greater than 1 means it is being consumed faster than expected.

---

## 16. Real-world example: e-commerce checkout

Suppose an online shopping application defines:

```text
SLO: 99.9% of checkout requests should succeed
```

Therefore:

```text
Error Budget = 0.1%
```

During the month:

```text
Total checkout requests = 10,000,000
Allowed failures = 10,000,000 × 0.1% = 10,000 failures
```

If the service has:

```text
Actual failures = 4,000
```

then it has consumed 40% of the error budget.

If another deployment causes:

```text
8,000 additional failures
```

then the team will exceed the monthly budget.

The team should investigate:

- what caused the failures?
- was there a deployment?
- was there a dependency failure?
- was there a database issue?
- was there a capacity problem?
- was the SLO definition correct?
- can the issue be prevented next time?

This is where error budgets become useful in real SRE work: they help quantify impact and guide engineering priorities.

---

## 17. Error budget as a decision-making tool

Error budgets are not just monitoring metrics.

They help teams make engineering decisions.

For example:

```text
Should we release this risky change?
 ↓
How much error budget remains?
 ↓
Is the service currently reliable?
 ↓
How quickly are we consuming the budget?
 ↓
What is the risk of this change?
```

So error budgets connect:

- reliability
- engineering
- product decisions
- release decisions

This is why they are an important part of practical SRE.

---

## 18. Common mistakes

### Mistake 1: Treating 100% availability as the default

100% availability is usually unrealistic.

Instead, define a realistic SLO based on:

- user expectations
- business requirements
- service criticality
- cost
- technical constraints

### Mistake 2: Setting an SLO without measuring the SLI

You need a reliable measurement before setting a meaningful target.

```text
No meaningful SLI
 ↓
No meaningful SLO
 ↓
No meaningful Error Budget
```

### Mistake 3: Treating the error budget as permission to fail

It does not mean:

> "We are allowed to break production."

It means:

> "We have explicitly accepted a defined level of unreliability based on our SLO."

### Mistake 4: Ignoring burn rate

Knowing that 50% of the budget is consumed is not enough.

You also need to know:

> how quickly the remaining budget is being consumed.

### Mistake 5: Automatically stopping every deployment

Do not blindly follow this pattern:

```text
Budget < X%
 ↓
Stop all deployments
```

Instead, consider:

- severity
- risk
- current incidents
- remaining budget
- burn rate
- business requirements
- nature of the change

### Mistake 6: Treating the error budget as fixed forever

An error budget depends on:

- SLO
- measurement window
- SLI definition

If any of these change, the budget may change too.

---

## 19. Interview answer (30 seconds)

> An error budget is the amount of unreliability a service can have while still meeting its SLO during a defined measurement period. For example, if our availability SLO is 99.9%, our error budget is 0.1%. Over a 30-day period, that is about 43 minutes of allowed unavailability. We use the error budget to balance reliability with feature delivery. If we are consuming the budget too quickly or have exhausted it, we usually prioritize reliability work and become more cautious about risky changes. Burn rate tells us how quickly we are consuming that budget.

---

## 20. Interview follow-up questions

### Beginner

- What is an error budget?
- How do you calculate it?
- What is the relationship between SLO and error budget?
- Give an example of a 99.9% SLO.
- Is error budget the same as downtime?
- Why do we need an error budget?

### Intermediate

- What happens when the error budget is exhausted?
- What is an error budget policy?
- How does error budget influence deployments?
- What is burn rate?
- Why is burn rate important?
- What is the difference between error budget and burn rate?
- What is the measurement window?

### Senior SRE / architect

- How would you design an error-budget policy for multiple services?
- How would you integrate error budgets into CI/CD?
- How would you prevent a team from gaming the SLO?
- How do you handle conflicting SLOs between dependent services?
- How do you decide whether an SLO is too strict?
- How would you use error budgets with canary deployments?
- How would you alert on rapid error-budget consumption?
- How would you determine whether an incident consumed too much error budget?
- What happens when a dependency consumes your service's error budget?

---

## 21. Remember this

Keep this mental model:

```text
SLI
What are we measuring?

 ↓

SLO
How reliable should it be?

 ↓

Error Budget
How much unreliability can we afford?

 ↓

Burn Rate
How quickly are we consuming it?

 ↓

Error Budget Policy
What should we do about it?
```

> SLO sets the target → error budget defines the tolerance → burn rate shows the speed → policy determines the action.

---

## 22. Key takeaways

If you remember only five things:

1. SLO defines the reliability target.
2. Error budget is the allowed amount of unreliability.
3. Error budget depends on the SLO and measurement window.
4. Burn rate tells us how quickly the budget is being consumed.
5. Error budget policy defines what the team should do when reliability starts suffering.

---

## 23. References

### Google SRE — Service Level Objectives
https://sre.google/sre-book/service-level-objectives/

### Google SRE Workbook — Error Budget Policy
https://sre.google/workbook/error-budget-policy/

### Google SRE — Implementing SLOs
https://sre.google/workbook/implementing-slos/

### Google SRE — Embracing Risk
https://sre.google/sre-book/embracing-risk/

### Google Cloud — Understanding Error Budget Overspend
https://cloud.google.com/blog/products/gcp/understanding-error-budget-overspend-cre-life-lessons

---

## What’s next?

After understanding error budget, the next foundation concept is:

**monitoring-and-observability.md**

We now know:

> what reliability target we want and how much unreliability we can tolerate.

The next question is:

> How do we know what is happening inside the system?

That leads naturally into:

```text
Monitoring
 ↓
Observability
 ↓
Metrics
 ↓
Logs
 ↓
Traces
 ↓
Signals
```

This connects the SRE foundation to tools such as Prometheus, Grafana, Splunk, Dynatrace, OpenTelemetry, and cloud monitoring platforms.


