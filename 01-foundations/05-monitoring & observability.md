# Day 5 — Monitoring & Observability 

> **Goal:** Understand how SREs monitor systems, investigate problems, and use observability to understand what is happening inside a system.

---

## 1. Monitoring vs Observability

These two terms are often used together, but they are not exactly the same.

### Monitoring

**Monitoring helps us detect known problems and alert when something needs attention.**

For example:

* CPU usage is consistently high
* Error rate increased
* Application is returning HTTP 500
* Disk space is almost full
* Request latency crossed a threshold

Monitoring usually works with **known conditions and predefined signals**.

A simple way to think about it:

> **Monitoring = "Is something wrong?"**

---

### Observability

**Observability helps us understand the behavior and internal state of a system, especially when investigating unexpected problems.**

For example:

> Users report that checkout is slow.

Monitoring might tell us:

```text
Checkout API latency > 3 seconds
```

Observability helps us investigate further:

```text
Checkout API
    ↓
Order Service
    ↓
Payment Service
    ↓
Database
```

We can follow the request and identify that:

```text
Payment Service
      ↓
Database query
      ↓
Slow query
      ↓
High latency
```

So:

> **Observability = "What is happening, and why?"**

---

## 2. Simple Difference

| Monitoring                             | Observability                              |
| -------------------------------------- | ------------------------------------------ |
| Detects known problems                 | Helps investigate system behavior          |
| Uses predefined signals and conditions | Helps explore unexpected problems          |
| Focuses on detecting issues            | Provides context for troubleshooting       |
| Can trigger alerts                     | Helps engineers understand the problem     |
| Answers "Is something wrong?"          | Helps answer "What is happening, and why?" |

A good SRE environment needs **both**.

---

## 3. The Traditional Three Signals of Observability

Metrics, logs, and traces are commonly described as the **three pillars of observability**.

```text
              Observability
                   |
        +----------+----------+
        |          |          |
      Metrics     Logs      Traces
        |          |          |
      Trends     Details    Request path
```

These signals complement each other.

> Modern observability can also include other telemetry and context, but metrics, logs, and traces are the foundation we should understand first.

---

## 4. Metrics 

Metrics are **numeric measurements collected over time**.

Examples:

```text
CPU Usage       → 75%
Memory Usage    → 68%
Request Rate    → 1,500 req/sec
Error Rate      → 2%
Latency         → 350 ms
```

Metrics are useful for understanding the **health and behavior of a system at scale**.

### Common metric types

#### Counter

A counter is a cumulative value that normally increases and may reset, for example when an application restarts.

Example:

```text
http_requests_total
```

It can represent the total number of requests received.

---

#### Gauge

A gauge is a value that can increase or decrease.

Examples:

```text
CPU usage
Memory usage
Active connections
Queue depth
```

---

#### Histogram

A histogram represents the **distribution of observed values**, such as request latency.

For example:

```text
Request latency

<100 ms       → 5,000 requests
100–250 ms    → 3,000 requests
250–500 ms    → 1,500 requests
>500 ms       → 500 requests
```

Histogram data can then be used to calculate values such as:

```text
P50
P90
P95
P99
```

This is useful because averages can hide slow requests.

---

## 5. Logs 

Logs are records of events that happened inside an application or system.

Example:

```text
2026-09-29 21:30:15
ERROR PaymentService
Payment request failed
transaction_id=12345
```

Logs can provide detailed context such as:

* What happened?
* When did it happen?
* Which service was involved?
* Which request or transaction was affected?
* What error occurred?

### Structured Logs

Instead of plain text:

```text
Payment failed for order 12345
```

we can use structured logging:

```json
{
  "service": "payment-service",
  "level": "ERROR",
  "order_id": "12345",
  "error": "payment_timeout"
}
```

Structured logs are easier to search, filter, and analyze.

---

## 6. Traces 

A trace follows a request as it travels through multiple services.

For example:

```text
User
  |
  v
API Gateway
  |
  v
Order Service
  |
  +------> Inventory Service
  |
  +------> Payment Service
  |
  v
Database
```

A distributed trace helps us understand:

* Which services participated?
* How long did each service take?
* Where did the request slow down?
* Where did the error occur?

### Example

Suppose the total request takes:

```text
Total request = 5 seconds

API Gateway       → 100 ms
Order Service     → 200 ms
Inventory Service → 150 ms
Payment Service   → 4.5 sec
Database          → 3.8 sec
```

Instead of saying:

> "The application is slow."

we can say:

> "Most of the latency is coming from the Payment Service's database interaction."

That is the power of distributed tracing.

---

## 7. Metrics + Logs + Traces Together

The real value comes from **correlating these signals**.

Imagine an alert:

```text
Checkout API error rate increased
```

### Step 1 — Metrics

Metrics tell us:

```text
Error rate increased from 0.5% → 8%
```

### Step 2 — Logs

Search application logs around the same time:

```text
Payment timeout
Database connection timeout
```

### Step 3 — Traces

Follow an affected request:

```text
Checkout
   ↓
Order Service
   ↓
Payment Service
   ↓
Database
```

Trace shows:

```text
Database call = 4.2 seconds
```

Now we have much better context for the investigation.

### Correlation

A useful production setup allows engineers to move between signals using identifiers such as:

```text
Request ID
Trace ID
Transaction ID
```

For example:

```text
Trace ID: abc123

Metric
→ Checkout latency increased

Log
→ Payment timeout

Trace
→ Payment Service → Database = 2.5 sec
```

This can significantly reduce investigation time.

---

## 8. The Four Golden Signals

For SRE, remember these four signals:

```text
1. Latency
2. Traffic
3. Errors
4. Saturation
```

These are commonly called the **Four Golden Signals**.

---

## Latency

How long does the system take to respond?

Example:

```text
P50 = 100 ms
P95 = 300 ms
P99 = 800 ms
```

---

## Traffic

How much demand is the system receiving?

Examples:

```text
Requests/sec
Transactions/sec
Messages/sec
Users/sec
```

---

## Errors

How many requests are failing?

Examples:

```text
HTTP 5xx
Application exceptions
Failed transactions
Timeouts
```

---

## Saturation

How close is a resource or system to its capacity, and is work starting to wait because of that limit?

Examples:

```text
CPU utilization       → 90%
CPU saturation        → Processes waiting for CPU

Connection pool usage → 95%
Queue depth            → 10,000
```

Remember:

> **Utilization tells us how much of a resource is being used. Saturation tells us how much work is waiting because the resource is constrained.**

### Easy way to remember

```text
Latency       → How slow?
Traffic       → How much?
Errors        → How broken?
Saturation    → How constrained?
```

---

## 9. RED Method

For services, another useful approach is the **RED method**:

```text
R → Rate
E → Errors
D → Duration
```

### Rate

How many requests are being processed?

### Errors

How many requests are failing?

### Duration

How long are requests taking?

Example dashboard:

```text
Request Rate       → 2,000 req/sec
Error Rate         → 1.5%
P95 Latency        → 450 ms
```

RED is particularly useful for monitoring **request-driven services**.

---

## 10. USE Method

The **USE method** is commonly useful for infrastructure resources:

```text
U → Utilization
S → Saturation
E → Errors
```

Example:

```text
CPU

Utilization → 85%
Saturation  → Processes waiting for CPU
Errors      → Hardware errors
```

A simple distinction:

```text
RED → Think services

USE → Think resources/infrastructure
```

---

## 11. Alerting 

Monitoring without useful alerting can create another problem:

> **Alert fatigue**

If engineers receive hundreds of alerts every day, important alerts can get lost in the noise.

A good alert should answer:

```text
What is wrong?
How serious is it?
Who needs to respond?
What should we investigate?
```

### Not every metric needs an alert

For example:

```text
CPU > 80%
```

may be useful for investigation, capacity planning, or early warning.

But by itself, it may not justify paging someone.

A better page might be:

```text
Checkout service has sustained resource saturation
for 15 minutes and request latency is above SLO.
```

The important principle is:

```text
Not every metric needs an alert.
Not every alert needs a page.
```

---

## 12. Symptoms vs Causes

One important SRE mindset:

> **Prefer user-impacting symptoms for paging when possible, while also using important resource and dependency signals for early warning and investigation.**

Example:

```text
Database CPU = 90%
```

This may not necessarily mean users are experiencing a problem.

But:

```text
Checkout error rate > SLO threshold
```

is directly connected to user impact.

The database CPU can still be useful for **investigation and early warning**, even if it does not directly trigger a page.

---

## 13. Page vs Ticket vs Dashboard

Not everything needs a page.

### Page

Something requires immediate human attention.

Example:

```text
Production checkout unavailable
```

### Ticket

Something needs attention but not immediately.

Example:

```text
Disk usage reached 75%
```

### Dashboard

Useful for investigation and understanding trends.

Example:

```text
CPU
Memory
Traffic
Latency
Errors
```

Think:

```text
Page      → Act now
Ticket    → Act later
Dashboard → Understand
```

> A dashboard is primarily for understanding and investigation; it is not a replacement for actionable alerting.

---

## 14. Observability Workflow

A practical SRE investigation often looks like this:

```text
Alert
  ↓
Confirm user impact
  ↓
Check service metrics
  ↓
Check logs
  ↓
Follow distributed trace
  ↓
Identify dependency
  ↓
Check recent changes
  ↓
Find root cause / contributing factors
  ↓
Mitigate
  ↓
Verify recovery
  ↓
Document / improve
```

Don't immediately jump into logs.

First understand:

> **What is the actual symptom?**

Then progressively add more detail.

---

## 15. Example: Checkout Is Slow

Imagine users report:

> "Checkout is taking too long."

### Step 1 — Metrics

```text
Checkout P95 latency

Before → 300 ms
Now    → 3 seconds
```

We know there is a latency problem.

### Step 2 — Traffic

```text
Traffic is normal.
```

So the problem may not be caused by a sudden traffic spike.

### Step 3 — Errors

```text
Error rate = normal
```

Interesting:

The service is returning successful responses, but they are slow.

### Step 4 — Traces

Trace shows:

```text
Checkout
  ↓
Payment Service
  ↓
Database
```

Database call is taking:

```text
2.5 seconds
```

### Step 5 — Logs

Logs show:

```text
Slow query detected
```

Now the investigation has moved from:

```text
"Checkout is slow"
```

to:

```text
"Checkout latency is primarily caused by a slow
database query in the payment path."
```

That's the value of observability.

---

## 16. Observability Tools

You don't need to memorize every tool.

**Understand the concepts first.**

Common tools include:

### Metrics

* Prometheus
* Grafana
* CloudWatch
* Dynatrace
* Datadog

### Logs

* Splunk
* Elasticsearch / Kibana
* Loki
* CloudWatch Logs
* Dynatrace

### Traces

* OpenTelemetry
* Jaeger
* Tempo
* Dynatrace
* Datadog

### OpenTelemetry

OpenTelemetry is important because it provides a vendor-neutral way to **instrument applications and collect/export telemetry** such as:

```text
Metrics
Logs
Traces
```

It can send telemetry to different observability backends.

> **OpenTelemetry is not an observability backend. It is a framework/toolkit for generating, collecting, and exporting telemetry.**

---

## 17. Observability vs APM

APM means **Application Performance Monitoring**.

APM typically focuses on application performance such as:

* Request performance
* Service dependencies
* Database calls
* Exceptions
* Distributed traces
* Application metrics

Observability is a broader concept.

It can include:

```text
Application
Infrastructure
Kubernetes
Network
Cloud
Database
Logs
Metrics
Traces
```

So:

> **APM can be part of an overall observability strategy.**

---

## 18. What Should an SRE Monitor?

Don't monitor everything just because you can.

Start with what matters to users and the business.

For a web application:

```text
User Experience
      ↓
Availability
Latency
Errors
Traffic
      ↓
Application
      ↓
Dependencies
      ↓
Infrastructure
```

Typical signals:

### Application

* Request rate
* Error rate
* Latency
* Exceptions
* Thread pools
* Connection pools

### Infrastructure

* CPU
* Memory
* Disk
* Network
* Load

### Dependencies

* Database latency
* Cache hit ratio
* External API latency
* Queue depth

### Kubernetes

* Pod health
* Restart count
* CPU / memory
* Node health
* Deployment status
* HPA behavior

---

## 19. Monitoring Should Support SLOs

Monitoring becomes much more meaningful when connected to **SLOs**.

Example:

```text
SLO:
99.9% of checkout requests should succeed
```

Monitoring should help us answer:

```text
Are we meeting the SLO?

How much error budget remains?

Is reliability getting better or worse?
```

This connects the concepts we learned in earlier days:

```text
User / Business Need
        ↓
SLI
        ↓
SLO
        ↓
Telemetry
        ↓
Monitoring & Alerting
        ↓
Incident Response
        ↓
Reliability Improvement
```

The important mindset is:

> **We don't monitor just because metrics exist. We monitor to understand whether the system is meeting its reliability objectives.**

---

## 20. Common Mistakes

### Mistake 1 — Monitoring everything

More dashboards ≠ better observability.

---

### Mistake 2 — Too many alerts

If everything alerts, nothing is important.

---

### Mistake 3 — Only monitoring infrastructure

CPU may be healthy while users are receiving errors.

---

### Mistake 4 — Looking only at averages

Average latency can hide slow requests.

Prefer percentiles such as:

```text
P50
P90
P95
P99
```

---

### Mistake 5 — No correlation

Metrics, logs, and traces should ideally allow engineers to move from one signal to another.

---

### Mistake 6 — No ownership

Every important alert should have a clear owner or escalation path.

---

### Mistake 7 — Collecting telemetry without a purpose

More telemetry is not automatically better.

Think about:

```text
What question are we trying to answer?

What signal helps answer it?

Who will use it?

What action will it trigger?
```

---

## 21. SRE Mental Model

When something goes wrong, think in this order:

```text
1. What are users experiencing?
            ↓
2. Is there measurable impact?
            ↓
3. Which service is affected?
            ↓
4. Which dependency is involved?
            ↓
5. What do metrics show?
            ↓
6. What do logs show?
            ↓
7. What do traces show?
            ↓
8. What changed recently?
            ↓
9. Can we mitigate quickly?
            ↓
10. How do we prevent recurrence?
```

This mindset is more valuable than memorizing tools.

---

## 22. Interview Questions

### Q1. What is the difference between monitoring and observability?

**Answer:**

> Monitoring helps detect known problems using predefined signals and conditions. Observability helps us understand system behavior and investigate unexpected problems. Monitoring helps answer "Is something wrong?" while observability helps answer "What is happening, and why?"

---

### Q2. What are the three pillars of observability?

**Answer:**

> Metrics, logs, and traces. Metrics show numerical trends, logs provide detailed event information, and traces show how requests move through distributed systems.

---

### Q3. What are the Four Golden Signals?

**Answer:**

> Latency, traffic, errors, and saturation. They provide a practical way to understand service health and user experience.

---

### Q4. What is the RED method?

**Answer:**

> RED stands for Rate, Errors, and Duration. It is commonly used to monitor request-driven services.

---

### Q5. What is the USE method?

**Answer:**

> USE stands for Utilization, Saturation, and Errors. It is commonly used to analyze infrastructure resources.

---

### Q6. Why shouldn't we alert on every metric?

**Answer:**

> Too many alerts create alert fatigue. Engineers may start ignoring alerts, including important ones. Alerts should generally represent actionable conditions that require attention.

---

### Q7. Why are percentiles useful for latency?

**Answer:**

> Averages can hide slow requests. Percentiles such as P95 and P99 help us understand the experience of slower requests and detect tail latency.

---

### Q8. How would you troubleshoot a slow API?

**Answer:**

> First I would confirm user impact and check latency and traffic metrics. Then I would check errors and service dependencies. I would use logs for detailed events and distributed traces to identify where the request is spending time. I would also check recent deployments or configuration changes and then mitigate the issue before doing deeper root-cause analysis.

---

### Q9. How do metrics, logs, and traces work together?

**Answer:**

> Metrics help identify that something is wrong, logs provide detailed information about events, and traces help identify where the problem occurs across distributed services. Correlating them using identifiers such as trace IDs or request IDs makes troubleshooting faster.

---

### Q10. What is the difference between utilization and saturation?

**Answer:**

> Utilization tells us how much of a resource is being used. Saturation tells us whether work is waiting because that resource is constrained or at capacity.

---

### Q11. Does every alert need to page an engineer?

**Answer:**

> No. A page should generally represent an actionable condition that requires immediate human attention. Other conditions can be handled through dashboards, tickets, or lower-priority notifications.

---

### Q12. What is OpenTelemetry?

**Answer:**

> OpenTelemetry is a vendor-neutral framework and ecosystem for instrumenting applications and collecting and exporting telemetry such as metrics, logs, and traces to observability backends.

---

## 23. What I Should Remember

If I only remember a few things from this topic:

```text
Monitoring
→ Detect known problems

Observability
→ Understand system behavior

Metrics
→ What is happening?

Logs
→ What happened?

Traces
→ Where did it happen?

Golden Signals
→ Latency + Traffic + Errors + Saturation

RED
→ Rate + Errors + Duration

USE
→ Utilization + Saturation + Errors

Good Alert
→ Actionable + Relevant + Owned

Good Observability
→ Helps reduce investigation time

SRE Mindset
→ Start with user impact, then investigate deeper
```

---

## 24. Day 5 Takeaway

> **Good observability is not about collecting more data.**
>
> It is about collecting the **right signals**, connecting them with useful context, and helping engineers understand **what is happening and why**.

As an SRE, the goal is not simply:

```text
"Monitor the infrastructure."
```

The goal is:

```text
"Understand system behavior,
detect user impact quickly,
reduce troubleshooting time,
and improve reliability."
```

---

## References

### Google SRE Book — Monitoring Distributed Systems

A foundational reference for understanding monitoring from an SRE perspective:

[Google SRE Book — Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/)

### Google SRE Workbook — Alerting

Useful for understanding practical alerting and what should require human attention:

[Google SRE Workbook — Alerting](https://sre.google/workbook/alerting/)

### OpenTelemetry Documentation

Official documentation for understanding telemetry, instrumentation, metrics, logs, and traces:

[OpenTelemetry Documentation](https://opentelemetry.io/docs/)

### Prometheus Documentation

Useful for learning metrics and monitoring concepts:

[Prometheus Documentation](https://prometheus.io/docs/introduction/overview/)

### Grafana Documentation

Useful for dashboards, visualization, and observability concepts:

[Grafana Documentation](https://grafana.com/docs/)

---

## Next Step

**Day 6 → Incident Management & Troubleshooting**

The next natural question after monitoring and observability is:

> **"We detected a problem. Now what should an SRE actually do?"**

That leads into:

```text
Alert
  ↓
Incident
  ↓
Triage
  ↓
Mitigation
  ↓
Recovery
  ↓
Root Cause Analysis
  ↓
Postmortem
  ↓
Prevent Recurrence
```

---

**SRE Zero to Hero **

**Learn → Practice → Break Things → Troubleshoot → Improve**
