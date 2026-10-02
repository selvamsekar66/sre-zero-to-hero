# Day 7 — Alerting

## What is Alerting?

**Alerting** is the process of automatically detecting a condition that needs attention and notifying the right person or team.

In SRE, monitoring tells us **what is happening**, while alerting tells us:

> "Something needs attention now."

For example:

- API error rate suddenly increases
- Application latency becomes very high
- A production service becomes unavailable
- Kubernetes pods are repeatedly crashing
- Disk usage reaches a critical level
- An SLO is being violated

The goal of alerting is **not to alert on everything**.

The goal is to alert when a human needs to take action.

---

## Monitoring vs Alerting

| Monitoring | Alerting |
|---|---|
| Collects and displays system information | Notifies someone when attention is required |
| Helps understand system health | Helps trigger action |
| Usually includes dashboards and metrics | Usually includes notifications/pages |
| Can be used for troubleshooting | Used when a condition needs attention |
| Example: CPU is 85% | Example: CPU is causing service impact |

### Simple example

Imagine an application normally has:

```text
Error Rate = 0.5%
```

Suddenly:

```text
Error Rate = 15%
```

A dashboard can show this increase.

An alert can notify the on-call engineer:

```text
CRITICAL: API error rate is above 10% for 5 minutes.
Service: Payment API
Environment: Production
Action: Investigate payment failures.
Runbook: <link>
```

---

# Why Do We Need Alerting?

A production system can have thousands of metrics.

An SRE cannot continuously watch every dashboard.

Alerting helps the team detect important problems automatically.

A good alert should help answer:

1. **What happened?**
2. **Which service is affected?**
3. **How serious is it?**
4. **Who needs to respond?**
5. **What should they do next?**

---

# The Most Important SRE Alerting Principle

> **Alert on symptoms, not every possible cause.**

Suppose users are experiencing slow responses.

There could be many possible causes:

```text
High latency
      |
      +-- CPU saturation
      +-- Database latency
      +-- Network problem
      +-- External API delay
      +-- Application issue
```

Instead of creating alerts for every possible cause, an SRE should consider alerting on the **user-visible symptom**.

For example:

```text
API latency > SLO threshold
```

The SRE can then investigate whether the cause is:

- CPU
- Database
- Network
- Application
- Dependency

This approach helps reduce unnecessary alerts.

Google's SRE guidance recommends keeping alerting simple, focusing on symptoms associated with user impact, and avoiding pages where there is nothing actionable to do.

---

# What Makes a Good Alert?

A good alert should be:

### 1. Actionable

Someone should know what action to take.

Bad:

```text
CPU = 80%
```

Better:

```text
High CPU is causing increased API latency.
Investigate application instances.
```

---

### 2. Relevant

The alert should represent a meaningful problem.

Not every unusual metric requires a page.

For example:

```text
CPU = 65%
```

may be perfectly normal.

But:

```text
API error rate = 20%
```

may require immediate investigation.

---

### 3. User-impacting

Whenever possible, alerts should be connected to customer or service impact.

Examples:

```text
High request error rate
High request latency
Service unavailable
SLO violation
```

This is generally more useful than alerting on every infrastructure metric.

---

### 4. Low Noise

An alert that fires constantly becomes noise.

Imagine receiving:

```text
Alert
Alert
Alert
Alert
Alert
Alert
```

every few minutes.

Eventually, engineers may start ignoring alerts.

This is known as:

**Alert Fatigue**

Alert fatigue can make it harder to notice a genuine production incident.

---

# Alert Severity

Organizations usually classify alerts based on their impact.

A simple example:

| Severity | Meaning | Example |
|---|---|---|
| Critical | Immediate action required | Production service unavailable |
| High | Significant impact | Error rate above critical threshold |
| Warning | Requires investigation | Disk usage approaching limit |
| Info | Useful information | Deployment completed |

The exact severity model depends on the organization.

A useful question is:

> **Does this alert require immediate human action?**

If the answer is no, it may not belong as a paging alert.

---

# Alert Thresholds

An alert normally has a condition that determines when it should fire.

Example:

```text
IF error_rate > 5%
FOR 5 minutes
THEN fire alert
```

Instead of:

```text
IF error_rate > 5%
THEN immediately alert
```

The duration helps prevent alerts caused by short-lived spikes.

### Example

Suppose an API has:

```text
12:00 → 1% errors
12:01 → 2% errors
12:02 → 8% errors
12:03 → 3% errors
```

A temporary spike may not justify waking up an engineer.

But if:

```text
Error rate > 5%
for 10 minutes
```

then the condition is more likely to represent a real problem.

Prometheus supports a `for` duration so that an alert can remain active for a specified period before becoming firing.

---

# Alert States

A common alert lifecycle looks like:

```text
Normal
   |
   v
Condition detected
   |
   v
Pending
   |
   | Condition continues
   v
Firing
   |
   | Condition clears
   v
Resolved
```

For example:

```text
Error rate > 5%
        |
        v
     Pending
        |
        | Still > 5%
        v
     Firing
        |
        | Error rate returns to normal
        v
    Resolved
```

The exact states depend on the monitoring and alerting system.

---

# Alert Components

A useful production alert should provide enough context for the engineer to start investigating.

Example:

```text
Alert: HighAPIErrorRate

Severity: Critical
Service: Payment API
Environment: Production

Condition:
Error rate > 5% for 5 minutes

Current Value:
12.4%

Impact:
Customers may experience failed payments.

Action:
Check API logs, recent deployments and downstream dependencies.

Dashboard:
<dashboard-link>

Runbook:
<runbook-link>

Owner:
Payments SRE Team
```

The engineer should not have to spend 10 minutes figuring out what the alert means.

---

# Alert Routing

Not every alert should go to every engineer.

A typical alerting flow looks like:

```text
Application / Infrastructure
          |
          v
       Metrics
          |
          v
   Alerting Rule
          |
          v
     Alert Manager
          |
    +-----+-----+
    |     |     |
    v     v     v
 Email  Slack  Pager
```

The routing system can decide:

- Which team receives the alert
- Severity
- Notification channel
- Whether alerts should be grouped
- Whether an alert should be temporarily silenced
- Whether duplicate alerts should be removed

For example:

```text
Payment API
     |
     v
Critical Alert
     |
     v
Payments On-Call
     |
     v
Pager
```

Prometheus Alertmanager provides capabilities such as grouping, deduplication, routing, silencing, and inhibition.

---

# Alert Grouping

Imagine 100 Kubernetes pods are running the same application.

A database failure occurs.

Without grouping:

```text
Pod 1 → Alert
Pod 2 → Alert
Pod 3 → Alert
...
Pod 100 → Alert
```

The engineer may receive 100 alerts.

With grouping:

```text
100 Pod Alerts
      |
      v
Grouped Alert
      |
      v
Database connectivity issue
```

This makes incident response easier.

Alertmanager supports grouping similar alerts into a single notification, which is particularly useful during large outages.

---

# Alert Suppression and Silencing

Sometimes an alert is expected.

For example:

```text
Planned database maintenance
```

During maintenance, several alerts may fire.

Instead of allowing all of them to notify engineers, alerts can be temporarily silenced or suppressed according to the organization's alerting setup.

This helps reduce unnecessary noise.

---

# Alert Fatigue

One of the biggest problems in production alerting is **too many alerts**.

Example:

```text
Monday:
120 alerts

Tuesday:
180 alerts

Wednesday:
250 alerts
```

But only 5 of them actually required human action.

This is a sign that the alerting system needs improvement.

Common causes include:

- Thresholds that are too sensitive
- Alerts for non-actionable conditions
- Duplicate alerts
- Temporary spikes
- Poor alert routing
- Missing grouping
- Alerts that don't represent user impact

A healthy alerting system aims for a high **signal-to-noise ratio**.

---

# Alert vs Ticket vs Dashboard

Not every condition should page an engineer.

A useful way to think about it:

| Situation | Possible Response |
|---|---|
| Immediate customer impact | Page |
| Important but not urgent | Ticket / notification |
| Useful information | Dashboard |
| Long-term trend | Dashboard / report |
| Planned maintenance | Silence / maintenance window |

The exact implementation varies between organizations.

---

# SLO-Based Alerting

One of the more important SRE concepts is **SLO-based alerting**.

Suppose:

```text
SLO = 99.9% availability
```

Instead of simply alerting:

```text
CPU > 80%
```

we can focus on whether the service is actually violating or approaching its reliability objective.

For example:

```text
SLO
 |
 v
Error Budget
 |
 v
Error Budget Burn
 |
 v
Alert
 |
 v
On-call Engineer
```

This connects alerting directly to service reliability.

Google's SRE Workbook discusses alerting on SLOs and using error-budget burn to identify significant reliability problems.

---

# Golden Signals and Alerting

The four Golden Signals are:

1. **Latency**
2. **Traffic**
3. **Errors**
4. **Saturation**

These can provide useful signals for alerting.

Example:

```text
Latency
   |
   +--> API response time too high

Traffic
   |
   +--> Unexpected traffic drop

Errors
   |
   +--> HTTP 5xx increasing

Saturation
   |
   +--> Resource capacity becoming exhausted
```

The important point is not to blindly create four alerts.

Instead, understand:

> **Which condition represents real service impact?**

---

# Example: Designing an API Alert

Imagine we operate:

```text
Payment API
```

The service normally has:

```text
Error Rate = 0.5%
Latency = 200 ms
Availability = 99.95%
```

We could design an alert such as:

```text
Alert Name:
PaymentAPIHighErrorRate

Condition:
Error rate > 5%

Duration:
5 minutes

Severity:
Critical

Impact:
Customers may experience payment failures.

Action:
Check recent deployments,
application logs,
database health,
and downstream dependencies.

Dashboard:
<Payment API Dashboard>

Runbook:
<Payment API Runbook>
```

This is much more useful than:

```text
CPU > 80%
```

because it provides context about the actual service problem.

---

# Alert Design Checklist

Before creating an alert, ask:

```text
[ ] Is this condition important?

[ ] Is it actionable?

[ ] Is there likely to be user impact?

[ ] Who should receive it?

[ ] What severity should it have?

[ ] Could a temporary spike trigger it?

[ ] Should there be a "for" duration?

[ ] Does the alert contain enough context?

[ ] Is there a dashboard link?

[ ] Is there a runbook?

[ ] Can the alert be tested?

[ ] Could this create alert fatigue?
```

---

# Beginner Interview Questions

### 1. What is alerting?

Alerting automatically detects important conditions and notifies the appropriate person or team so that action can be taken.

### 2. What is alert fatigue?

Alert fatigue occurs when engineers receive too many unnecessary or non-actionable alerts, making it harder to notice important incidents.

### 3. What makes a good alert?

A good alert should be actionable, relevant, meaningful, and contain enough context for the engineer to investigate.

### 4. Should we alert on every metric?

No.

Alerting should focus on conditions that require attention, especially conditions related to user or service impact.

### 5. What is the difference between a warning and a critical alert?

A warning generally indicates a condition that needs investigation but may not require immediate action.

A critical alert usually indicates a significant condition requiring immediate response.

### 6. Why use a duration such as `for 5m`?

It helps prevent temporary spikes or short-lived conditions from immediately triggering an alert.

### 7. What is alert grouping?

Alert grouping combines related alerts into a smaller number of notifications so that engineers are not overwhelmed by duplicate alerts.

### 8. What is alert suppression or silencing?

It prevents selected alerts from generating notifications when the alerts are known to be unnecessary, such as during planned maintenance.

### 9. What is SLO-based alerting?

It means designing alerts around service reliability objectives and their impact on the error budget rather than relying only on infrastructure thresholds.

### 10. What is the difference between an alert and a dashboard?

A dashboard helps engineers observe and investigate system behavior.

An alert notifies engineers when a condition requires attention.

---

# SRE Interview Scenario

### Scenario

You receive 500 alerts during a production incident.

How would you approach it?

A good starting approach:

```text
1. Identify the primary customer-facing symptom.

2. Group related alerts.

3. Identify whether multiple alerts have the same root cause.

4. Check service-level signals such as:
   - Error rate
   - Latency
   - Traffic
   - Saturation

5. Check recent deployments or configuration changes.

6. Check dependencies such as:
   - Database
   - Network
   - External APIs

7. Identify the alert that represents the actual impact.

8. Investigate using dashboards, logs and traces.

9. After recovery, review the alerting design.

10. Reduce duplicate or non-actionable alerts.
```

The important SRE mindset is:

> **Don't just fix the incident. Improve the signal that helps you detect the next incident.**

---

# Practical Example with Prometheus

Prometheus allows alerting rules to be defined using PromQL.

A simplified example:

```yaml
groups:
  - name: api-alerts
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High API error rate"
          description: "The API has a high 5xx error rate."
```

Conceptually:

```text
Prometheus
    |
    | evaluates rule
    v
Condition is true
    |
    v
Alert becomes Pending
    |
    | remains true for 5 minutes
    v
Alert becomes Firing
    |
    v
Alertmanager
    |
    v
Notification
```

Prometheus alerting rules support expressions, labels, annotations, and durations such as `for`.

---

# Key Takeaways

If you remember only these points, remember these:

```text
1. Alerting is about action, not information.

2. Don't alert on everything.

3. Prefer user-impacting symptoms over every possible cause.

4. Keep alert noise low.

5. Every paging alert should ideally be actionable.

6. Use appropriate severity.

7. Use duration/thresholds carefully to avoid false positives.

8. Provide useful context, dashboards and runbooks.

9. Group and route alerts appropriately.

10. Use SLOs and error budgets to build reliability-focused alerts.
```

---

# References

### Google SRE

- [Google SRE — Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/)
- [Google SRE Workbook — Monitoring](https://sre.google/workbook/monitoring/)
- [Google SRE Workbook — Alerting on SLOs](https://sre.google/workbook/alerting-on-slos/)
- [Google SRE Workbook — On-Call](https://sre.google/workbook/on-call/)

### Prometheus

- [Prometheus — Alerting Rules](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/)
- [Prometheus — Alerting Overview](https://prometheus.io/docs/alerting/latest/overview/)
- [Prometheus — Alertmanager](https://prometheus.io/docs/alerting/latest/alertmanager/)
- [Prometheus — Alerting Best Practices](https://prometheus.io/docs/practices/alerting/)

---

## Day 7 Interview Mindset

When an interviewer asks:

> **"How would you design alerting for a production application?"**

Don't start by saying:

```text
CPU > 80%
Memory > 90%
Disk > 80%
```

Start with:

```text
What is the user impact?
What should wake up an engineer?
What can be investigated later?
How do we avoid alert fatigue?
How do we connect the alert to an SLO?
What information does the engineer need to take action?
```

That is the difference between **monitoring a system** and **thinking like an SRE**.