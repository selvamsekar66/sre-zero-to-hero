# SLI, SLO & SLA

Reliability is not just about saying:

> "Our application is up."

The more important question is:

> **"Is the application working well for our users?"**

This is where **SLI, SLO and SLA** come into the picture.

They help an SRE team move from:

**"We think the system is reliable."**

to:

**"We can measure reliability, define a target, and make decisions based on it."**

---

# 1. The Big Picture

Think about the three terms like this:

```text
                 USER EXPERIENCE
                       │
                       ▼
                ┌─────────────┐
                │     SLI     │
                │   Measure   │
                └──────┬──────┘
                       │
                       ▼
                ┌─────────────┐
                │     SLO     │
                │   Target    │
                └──────┬──────┘
                       │
                       ▼
                ┌─────────────┐
                │     SLA     │
                │  Agreement  │
                └─────────────┘
```

In simple words:

| Term    | Simple meaning                         |
| ------- | -------------------------------------- |
| **SLI** | What do we measure?                    |
| **SLO** | What target do we want?                |
| **SLA** | What have we agreed with the customer? |

A simple way to remember:

> **SLI = Measurement**
> **SLO = Target**
> **SLA = Agreement**

---

# 2. What is an SLI?

**SLI = Service Level Indicator**

An SLI is a **measurement of an aspect of a service that matters to users**.

It tells us what is actually happening.

Common examples include:

* Availability
* Latency
* Error rate
* Successful requests
* Throughput
* Data freshness
* Durability
* Correctness

The important point is:

> **An SLI should measure something meaningful about the service, not simply something that is easy to collect.**

For example, CPU utilization may be useful for troubleshooting, but it is not automatically an SLI.

---

## Good Events and Valid Events

In many real-world systems, an SLI is calculated using:

```text
Good Events
───────────────
Valid Events
```

For example:

```text
100,000 valid requests

99,500 successful requests
500 failed requests
```

Then:

```text
SLI = 99,500 / 100,000

    = 99.5%
```

The definition of a **valid event** and a **good event** is important.

For example, an invalid request caused by a client mistake may not be treated the same way as a valid request that failed because of a service problem.

This is why defining the SLI carefully matters.

---

# 3. A Simple SLI Example

Suppose we have an online shopping API.

During one hour:

* 100,000 valid requests were received
* 99,500 requests were successful

We can calculate an availability SLI:

```text
Successful requests
─────────────────── × 100
Valid requests

99,500
────── × 100 = 99.5%
100,000
```

So our measured availability is:

**99.5%**

That is the **SLI**.

> **SLI tells us what the service actually delivered.**

---

# 4. Common SLI Examples

Different systems need different SLIs.

## User-facing application

Typical SLIs:

* Availability
* Latency
* Error rate

Example:

```text
Availability = 99.95%
p95 latency  = 250 ms
Error rate   = 0.05%
```

---

## API

We might measure:

```text
Successful valid HTTP requests
───────────────────────────────
Total valid HTTP requests
```

This gives us a success-rate SLI.

---

## Data pipeline

For a data pipeline, availability may not be the most useful measurement.

We might instead measure:

```text
Records successfully processed
───────────────────────────────
Records expected
```

We could also measure:

* Processing latency
* Data freshness
* Processing success rate

---

## Storage system

Useful indicators may include:

* Read latency
* Write latency
* Availability
* Durability

The important point is:

> **Don't measure something simply because your monitoring tool can measure it. Measure something that tells you about the reliability users actually experience.**

---

# 5. What is an SLO?

**SLO = Service Level Objective**

An SLO is the **target we want our SLI to achieve**.

Remember:

```text
SLI = What actually happened?

SLO = What level do we want?
```

### Example

Our SLI tells us:

```text
Availability = 99.5%
```

We define an SLO:

```text
Availability >= 99.9%
```

Now we have a clear target.

```text
Actual SLI     → 99.5%

SLO target     → 99.9%

Result         → SLO not met
```

This is much more useful than simply saying:

> "The application had some downtime."

We now have an objective way to discuss reliability.

---

# 6. SLO Measurement Window

An SLO needs a **measurement window**.

For example:

```text
SLO:
99.9% availability

Measurement window:
30 days
```

The SLI is measured during that period and then compared with the SLO.

Common measurement windows include:

* 7 days
* 28 days
* 30 days

The exact window depends on the service and how the team wants to manage reliability.

For example:

```text
99.9% SLO
    ↓
Measured over 30 days
    ↓
Compare actual SLI with target
    ↓
SLO met or SLO missed
```

The measurement window becomes especially important when we discuss **error budgets and burn rate**.

---

# 7. SLI vs SLO

This is one of the most important concepts to understand.

|          | SLI                   | SLO                  |
| -------- | --------------------- | -------------------- |
| Meaning  | Measurement           | Target               |
| Question | What happened?        | What do we want?     |
| Example  | 99.7% availability    | 99.9% availability   |
| Type     | Actual value          | Desired value        |
| Used for | Measuring reliability | Managing reliability |

### Easy way to remember

> **SLI = Speedometer reading**

> **SLO = Speed limit**

The speedometer tells you how fast you are going.

The speed limit tells you the acceptable target.

---

# 8. What is an SLA?

**SLA = Service Level Agreement**

An SLA is a **formal agreement between a service provider and a customer or consumer about service commitments**.

An SLA may include:

* Availability or reliability commitments
* Measurement rules
* Service conditions
* Exclusions
* Remedies or consequences when commitments are not met

For example:

```text
Service availability:
99.9%

Measurement period:
Monthly

If the commitment is not met:
Customer may receive service credits
```

The key difference is that an SLA is a **business or customer agreement**, not simply an engineering target.

SRE teams can help define the technical measurements and understand the operational impact, but business and commercial stakeholders are usually involved in defining the SLA.

---

# 9. SLI vs SLO vs SLA

Let's put everything together.

| Concept | Question                               | Example                                  |
| ------- | -------------------------------------- | ---------------------------------------- |
| **SLI** | What happened?                         | 99.95% availability                      |
| **SLO** | What target do we want?                | 99.9% availability                       |
| **SLA** | What have we agreed with the customer? | 99.9% availability with defined remedies |

### One simple example

Imagine an online banking application.

### SLI

```text
99.96% of valid requests completed successfully
```

### SLO

```text
At least 99.95% of valid requests
should complete successfully each month.
```

### SLA

```text
The customer agreement commits to
99.9% availability.

If the commitment is missed,
defined remedies may apply.
```

---

# 10. Why Does SRE Care About SLOs?

This is where SLOs become more interesting.

Without an SLO, teams may argue about reliability using opinions.

For example:

> Developer: "The application is fine."

> Operations: "We had five incidents this month."

> Business: "Customers are complaining."

Everyone may have a different definition of "reliable."

An SLO gives everyone a common measurement.

```text
                 Reliability discussion

                        │
                        ▼

                 ┌─────────────┐
                 │     SLI     │
                 │   Measure   │
                 └──────┬──────┘
                        │
                        ▼
                 ┌─────────────┐
                 │     SLO     │
                 │    Target   │
                 └──────┬──────┘
                        │
                 ┌──────┴──────┐
                 ▼             ▼
              Within        Outside
              target         target
                 │             │
                 ▼             ▼
              Continue      Investigate /
              normally       take action
```

This makes reliability more **data-driven**.

---

# 11. Why 100% Availability Is Usually Not the Goal

At first, 100% availability sounds perfect.

But achieving and maintaining 100% availability can require disproportionate engineering effort, complexity and cost.

Consider:

```text
99%
99.9%
99.99%
99.999%
```

Each additional "9" reduces the amount of acceptable downtime.

For example, over a 30-day month:

| Availability | Approx. monthly downtime |
| ------------ | -----------------------: |
| 99%          |                   7h 12m |
| 99.9%        |                  43m 12s |
| 99.99%       |                   4m 19s |
| 99.999%      |                      26s |

These numbers are approximate and assume a 30-day month.

The important lesson is not to blindly chase more nines.

Instead ask:

> **How reliable does this service need to be for its users and business?**

The answer depends on:

* User expectations
* Business impact
* Service criticality
* Cost
* Engineering effort
* Risk

---

# 12. A Short Introduction to Error Budgets

Once we have an SLO, we can understand how much unreliability the target allows.

For a percentage-based SLO:

```text
Error Budget = 100% - SLO
```

For example:

```text
SLO = 99.9%

Error Budget = 100% - 99.9%

             = 0.1%
```

For a 30-day month:

```text
99.9% SLO
    ↓
0.1% error budget
    ↓
≈ 43 minutes
```

This means the service has approximately 43 minutes of allowable unavailability during that 30-day period, assuming availability is measured continuously across the full window.

Error budgets become much more useful when we use them to make engineering decisions.

We'll explore that in detail in the next chapter.

---

# 13. Availability Is Not the Only SLO

A common beginner mistake is:

> "Our SLO is 99.9% uptime."

Reliability is broader than uptime.

Consider an application that is technically "up" but takes 30 seconds to respond.

Is it really providing a good service?

Probably not.

That's why we may define multiple SLOs.

Example:

```text
Availability:
99.9% of valid requests succeed

Latency:
99% of requests complete within 500 ms

Error rate:
Less than 0.1% of valid requests fail
```

The exact targets depend on the service and its users.

---

# 14. Think Like an SRE

When designing SLOs, don't start with:

> "What metrics do we already have?"

Start with:

> **"What does the user care about?"**

For example:

### User says:

> "I want to complete my payment."

The SRE may think:

```text
Can the request succeed?
        ↓
Availability SLI
        ↓
Can it complete quickly enough?
        ↓
Latency SLI
        ↓
Did the correct transaction happen?
        ↓
Correctness SLI
```

This is a very important SRE mindset.

> **Infrastructure metrics are not automatically user-centric reliability metrics.**

A server can have healthy CPU and memory while the user-facing payment service is failing.

---

# 15. Percentiles and Latency

Latency is usually not well represented by a simple average.

Imagine 100 requests:

```text
95 requests → 100 ms
5 requests  → 5 seconds
```

An average can hide the fact that some users experienced very slow requests.

This is why SRE teams often use percentiles.

### Common percentiles

```text
p50 → 50% of requests are at or below this latency
p95 → 95% of requests are at or below this latency
p99 → 99% of requests are at or below this latency
```

Example:

```text
p50 = 100 ms
p95 = 300 ms
p99 = 800 ms
```

This helps us understand **tail latency** — the experience of the slower requests.

For example:

> p99 = 800 ms

means approximately 99% of measured requests completed in 800 ms or less, while the slowest 1% took longer.

---

# 16. A Practical Example

Let's design reliability measurements for an e-commerce application.

## Step 1 — Understand the user

The user wants to:

```text
Browse products
      ↓
Add product to cart
      ↓
Make payment
      ↓
Receive confirmation
```

## Step 2 — Identify what matters

The user cares about:

* Requests succeeding
* Pages responding quickly
* Payments completing correctly

## Step 3 — Define SLIs

### Availability SLI

```text
Successful requests
───────────────────
Valid requests
```

### Latency SLI

```text
Request response time
```

### Payment correctness SLI

```text
Successful valid payments
──────────────────────────
Valid payment attempts
```

## Step 4 — Define SLOs

For example:

```text
Availability:
99.9%

Latency:
99% of requests < 500 ms

Payment success:
99.95%
```

These are example targets only.

Real targets should be chosen based on:

* User needs
* System behavior
* Business requirements
* Service criticality
* Cost and risk

## Step 5 — Evaluate the SLO

Suppose we measure:

```text
Availability SLI = 99.95%

Availability SLO = 99.9%
```

Then:

```text
99.95% > 99.9%

SLO = MET
```

This gives the team an objective way to discuss reliability.

---

# 17. SLI → SLO → Error Budget → Action

This is one of the most useful mental models in SRE.

```text
             USER NEED
                 │
                 ▼
              ┌─────┐
              │ SLI │
              └──┬──┘
                 │
            Measurement
                 │
                 ▼
              ┌─────┐
              │ SLO │
              └──┬──┘
                 │
               Target
                 │
                 ▼
          ┌─────────────┐
          │ Error Budget│
          └──────┬──────┘
                 │
                 ▼
             DECISION
                 │
        ┌────────┴────────┐
        ▼                 ▼
   Budget available   Budget exhausted
        │                 │
        ▼                 ▼
   Continue planned    Prioritize
   engineering work    reliability work
```

This is the bridge between **monitoring and engineering decisions**.

The next chapter will explore this in more detail.

---

# 18. SLO Is Not Just a Dashboard Number

An SLO should help answer questions such as:

* Should we release this change?
* Do we need to improve reliability?
* Should we investigate latency?
* Are we consuming too much error budget?
* Should reliability work take priority?
* Are our current targets still meaningful?

If an SLO never changes a decision, it is worth asking whether that SLO is actually useful.

A good SLO should help teams make better engineering decisions.

---

# 19. Common Mistakes

## ❌ Mistake 1 — Everything becomes an SLI

You may have hundreds of metrics:

```text
CPU
Memory
Disk
Network
Pods
Containers
Threads
GC
Database connections
...
```

Not all of them are SLIs.

They can be useful **diagnostic metrics**, but an SLI should represent an important aspect of service reliability.

---

## ❌ Mistake 2 — Setting SLOs based only on current performance

Suppose the application currently achieves:

```text
99.99%
```

That does not automatically mean the SLO should be:

```text
99.99%
```

The target should reflect what users need and what the service should provide.

---

## ❌ Mistake 3 — Making every SLO 99.999%

More nines are not automatically better.

Higher reliability can require:

* More infrastructure
* More engineering effort
* More operational complexity
* Higher cost

The target should match the service's needs.

---

## ❌ Mistake 4 — Confusing SLO and SLA

Remember:

```text
SLO → Reliability target

SLA → Customer/business agreement
```

They are related, but they are not the same thing.

---

## ❌ Mistake 5 — Measuring only infrastructure health

A server can be healthy while the user experience is broken.

For example:

```text
CPU        → 30%
Memory     → 50%
Disk       → 40%

But...

Payment API → failing
```

Infrastructure looks healthy.

The service is not.

This is why SRE focuses on **service reliability from the user's perspective**.

---

# 20. A Simple SRE Checklist

When defining SLI/SLOs for a service, ask:

### Understand the service

* Who uses the service?
* What is the most important user journey?
* What does failure look like?

### Define SLIs

* What should we measure?
* Does the measurement represent user experience?
* What are the good events?
* What are the valid events?
* Is the measurement reliable?

### Define SLOs

* What target should we achieve?
* What measurement window should we use?
* Is the target realistic?
* Does the target reflect user expectations?

### Think about the error budget

* How much unreliability does the SLO allow?
* How much budget has been consumed?
* What decisions should change when the budget is exhausted?

### Review regularly

* Is the SLO still meaningful?
* Are users actually experiencing the reliability we intended?
* Do we need to refine the measurement?

---

# 21. SRE Mental Model

Remember this simple chain:

```text
USER NEED
   ↓
WHAT MATTERS?
   ↓
SLI
   ↓
WHAT TARGET?
   ↓
SLO
   ↓
HOW MUCH FAILURE IS ACCEPTABLE?
   ↓
ERROR BUDGET
   ↓
WHAT SHOULD WE DO?
   ↓
ENGINEERING DECISION
```

And separately:

```text
CUSTOMER AGREEMENT
       ↓
      SLA
       ↓
 Commitments +
   Conditions +
   Remedies
```

---

# 22. Interview Perspective

These are common questions you should be able to answer after this chapter.

### Q1. What is the difference between SLI and SLO?

**Answer:**

> An SLI is a measurement of service reliability, while an SLO is the target we set for that measurement.

---

### Q2. What is an SLA?

**Answer:**

> An SLA is a customer or business agreement that defines service commitments and may include remedies when those commitments are not met.

---

### Q3. What is an error budget?

**Answer:**

> An error budget represents the amount of unreliability allowed by an SLO during its measurement window.

---

### Q4. Why shouldn't every metric become an SLI?

**Answer:**

> Because an SLI should represent something meaningful about the user's experience or service reliability. Many infrastructure metrics are useful for diagnosis but are not direct measures of user-facing reliability.

---

### Q5. Why use p95 or p99 instead of average latency?

**Answer:**

> Percentiles help us understand tail latency and expose slow requests that an average can hide.

---

# 23. Key Takeaways

If you remember only five things from this chapter:

### 1. SLI measures

> **What is actually happening?**

### 2. SLO defines the target

> **What level of reliability do we want?**

### 3. SLA defines an agreement

> **What have we committed to the customer?**

### 4. Error budget connects reliability with engineering decisions

> **How much unreliability does our SLO allow?**

### 5. Start with the user

> **Don't measure everything. Measure what matters.**

---

# 24. What's Next?

Now that we understand how reliability is **measured and defined**, the next question is:

> **What happens when the service starts consuming its reliability budget?**

That leads us to one of the most important SRE concepts:

### [Error Budgets & Reliability Decisions](04-error-budgets.md)

In the next chapter, we will explore:

* How error budgets work
* How teams track budget consumption
* What happens when the budget is healthy
* What happens when the budget is exhausted
* How error budgets influence release and reliability decisions

---

## References

* [Google SRE Book — Service Level Objectives](https://sre.google/sre-book/service-level-objectives/) — SLI, SLO, SLA and practical SLO design.
* [Google SRE Workbook — Implementing SLOs](https://sre.google/workbook/implementing-slos/) — Practical guidance for implementing and refining SLOs.
* [Google SRE Book — Embracing Risk](https://sre.google/sre-book/embracing-risk/) — Reliability, risk and error budgets.
* [Google SRE Book — Service Best Practices](https://sre.google/sre-book/service-best-practices/) — SLOs, user-focused reliability and error budgets.
