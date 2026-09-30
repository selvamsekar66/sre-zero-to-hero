# Day 6 — Golden Signals

## What are Golden Signals?

The **Golden Signals** are four key metrics that help SREs understand the health and performance of a system.

They are:

1. **Latency**
2. **Traffic**
3. **Errors**
4. **Saturation**

These four signals give us a simple way to answer:

> "Is my system healthy, and if it is not, what is going wrong?"

The Golden Signals were popularized by the Google SRE approach to monitoring.

---

## Why do we need Golden Signals?

Modern applications can have hundreds or thousands of metrics.

For example:

* CPU usage
* Memory usage
* Disk usage
* Network traffic
* Request count
* Response time
* Error count
* Database connections
* Queue length
* Thread count

Looking at everything at the same time can make troubleshooting difficult.

Golden Signals help us start with the **most important signals from the user's and system's perspective**.

A simple way to remember them:

```text
Traffic     → How much work is coming in?
Errors      → How much work is failing?
Latency     → How long is the work taking?
Saturation  → How close are we to running out of capacity?
```

---

## 1. Latency

**Latency** is the time taken to process a request.

For example:

```text
User sends request
       |
       v
Application processes request
       |
       v
Response returned

Total time = 250 ms
```

The latency is **250 milliseconds**.

### Example

Suppose an API normally responds in:

```text
100 ms
120 ms
110 ms
130 ms
```

Suddenly, the response time becomes:

```text
800 ms
950 ms
1200 ms
```

Users may start experiencing slow responses.

This could indicate problems such as:

* Slow database queries
* High CPU usage
* Network latency
* External dependency delays
* Garbage collection
* Thread or connection pool exhaustion

### Important

Do not look only at average latency.

For example:

```text
Average latency = 200 ms
```

This might look healthy, but some users could still be experiencing very slow requests.

SREs commonly use **percentiles** such as:

```text
p50 → 50% of requests are faster than this
p95 → 95% of requests are faster than this
p99 → 99% of requests are faster than this
```

Example:

```text
p50 = 100 ms
p95 = 300 ms
p99 = 900 ms
```

This tells us that a small percentage of requests are taking significantly longer.

---

## 2. Traffic

**Traffic** tells us how much demand the system is receiving.

For an HTTP API, traffic could be:

```text
Requests per second (RPS)
```

Example:

```text
Normal traffic = 500 requests/second

Current traffic = 2,000 requests/second
```

The application is receiving much more traffic than normal.

Traffic can be measured differently depending on the system.

| System           | Example Traffic Metric |
| ---------------- | ---------------------- |
| HTTP API         | Requests/second        |
| Database         | Queries/second         |
| Messaging system | Messages/second        |
| Storage          | Operations/second      |
| Network          | Bytes/second           |

### Why is Traffic important?

Traffic helps us understand whether a change in system behavior is related to increased demand.

For example:

```text
Traffic increases
       ↓
CPU increases
       ↓
Latency increases
       ↓
Errors increase
```

Without looking at traffic, we might incorrectly assume that the application itself suddenly became inefficient.

---

## 3. Errors

**Errors** tell us how many requests are failing.

For an HTTP application, errors could include:

```text
HTTP 4xx
HTTP 5xx
```

Examples:

```text
400 → Bad Request
401 → Unauthorized
403 → Forbidden
404 → Not Found
500 → Internal Server Error
502 → Bad Gateway
503 → Service Unavailable
504 → Gateway Timeout
```

However, not every 4xx response necessarily represents a system failure.

For example:

```text
404 Not Found
```

could be caused by a user requesting a resource that does not exist.

A better approach is to define what counts as an **error for your service**.

### Example

Suppose an API receives:

```text
10,000 requests
```

and:

```text
200 requests failed
```

Error rate:

```text
200 / 10,000 × 100

= 2%
```

So the error rate is **2%**.

---

## 4. Saturation

**Saturation** tells us how "full" or constrained a system is.

Think about a cup of water.

```text
20% full  → Plenty of capacity
60% full  → Normal
90% full  → Getting close to the limit
100% full → No additional capacity
```

The same idea applies to computing resources.

Examples include:

* CPU utilization
* Memory utilization
* Disk utilization
* Database connection pools
* Thread pools
* Queue depth
* Network bandwidth
* Container resource limits

### Example

Suppose a server has:

```text
CPU = 95%
Memory = 92%
Database connections = 98% used
```

The system may still be working, but it has very little remaining capacity.

A sudden increase in traffic could push the system into failure.

---

## Putting the Four Signals Together

The real power of Golden Signals comes from looking at them together.

Consider this example:

```text
Traffic
   ↑
   |
   |        /\
   |       /  \
   |______/    \____

Latency
   ↑
   |          /\
   |         /  \
   |________/    \____

Errors
   ↑
   |          /\
   |         /  \
   |________/    \____

Saturation
   ↑
   |          /\
   |         /  \
   |________/    \____
```

This could indicate:

```text
Traffic increased
       ↓
Resources became saturated
       ↓
Requests became slower
       ↓
Some requests started failing
```

This gives the SRE a starting point for investigation.

---

## Real-World Example

Imagine an e-commerce application.

Normally:

```text
Traffic       = 1,000 RPS
Latency p95   = 200 ms
Error rate    = 0.2%
CPU           = 50%
```

During a major sale:

```text
Traffic       = 5,000 RPS
Latency p95   = 1,500 ms
Error rate    = 8%
CPU           = 95%
```

The Golden Signals immediately show that the system is under significant load.

A possible investigation path could be:

```text
Traffic increased
       ↓
CPU increased
       ↓
Application became saturated
       ↓
Latency increased
       ↓
Errors increased
```

The next step would be to investigate capacity, scaling, dependencies, database performance, and application behavior.

---

## Golden Signals vs Infrastructure Metrics

Golden Signals are not a replacement for infrastructure metrics.

They answer different questions.

### Golden Signals

Focus on **service health and user experience**:

```text
Traffic
Errors
Latency
Saturation
```

### Infrastructure Metrics

Help us understand **why** the service might be unhealthy:

```text
CPU
Memory
Disk
Network
Container resources
Database connections
```

For example:

```text
Golden Signal:

Latency increased
       ↓
Infrastructure investigation:

CPU = 95%
```

The infrastructure metric helps explain the Golden Signal.

---

## Golden Signals and SRE Monitoring

A basic SRE dashboard can start with the four Golden Signals.

Example:

```text
------------------------------------------------
              SERVICE HEALTH
------------------------------------------------

Traffic
1,200 requests/sec

Latency
p95: 250 ms

Errors
0.5%

Saturation
CPU: 65%
------------------------------------------------
```

This gives an engineer a quick overview of the service.

If something looks abnormal, we can then drill down into more detailed metrics, logs, and traces.

---

## Golden Signals with Logs and Traces

Golden Signals tell us **that something is wrong**.

Other observability signals help us understand **why**.

A typical troubleshooting flow can be:

```text
Golden Signals
      ↓
Identify abnormal behavior
      ↓
Metrics
      ↓
Understand the trend
      ↓
Logs
      ↓
Find error details
      ↓
Traces
      ↓
Find the slow/failing component
```

For example:

```text
Latency increased
       ↓
Check metrics
       ↓
Database latency increased
       ↓
Check logs
       ↓
Slow query errors found
       ↓
Check distributed trace
       ↓
Database query identified
```

This is where **observability** becomes useful.

---

## Golden Signals vs RED Method

You may also come across the **RED method**.

RED stands for:

```text
R → Rate
E → Errors
D → Duration
```

It is commonly used for monitoring request-driven services.

There is some overlap:

| Golden Signals | RED                   |
| -------------- | --------------------- |
| Traffic        | Rate                  |
| Errors         | Errors                |
| Latency        | Duration              |
| Saturation     | Not directly included |

The RED method focuses mainly on request behavior, while Golden Signals also include **system saturation**.

---

## Golden Signals vs USE Method

Another monitoring approach is the **USE method**.

USE stands for:

```text
U → Utilization
S → Saturation
E → Errors
```

It is especially useful for infrastructure and resource analysis.

For example:

```text
CPU

Utilization → 90%
Saturation  → High load / CPU queue
Errors      → CPU-related errors
```

Golden Signals, RED, and USE are complementary approaches. They are not competing monitoring systems.

---

## Beginner Checklist

When monitoring a service, ask:

### Traffic

* How much traffic is the service receiving?
* Is traffic normal compared with the baseline?
* Did traffic suddenly increase or decrease?

### Errors

* Are requests failing?
* What is the error rate?
* Which error types are increasing?
* Are the errors coming from the application or a dependency?

### Latency

* Are requests becoming slower?
* What is the p50 latency?
* What is the p95 latency?
* What is the p99 latency?

### Saturation

* Is CPU becoming constrained?
* Is memory becoming constrained?
* Are connection pools filling up?
* Are queues growing?
* Are we approaching resource limits?

---

## Simple Mental Model

Remember the Golden Signals like this:

```text
             GOLDEN SIGNALS

                  SERVICE
                    |
        +-----------+-----------+
        |           |           |
     Traffic     Errors      Latency
                    |
               Saturation
```

Or simply:

> **Traffic tells us how much work is coming in.**
> **Errors tell us how much work is failing.**
> **Latency tells us how long the work takes.**
> **Saturation tells us how close we are to our limits.**

---

## Key Takeaways

1. Golden Signals are **Latency, Traffic, Errors, and Saturation**.
2. They provide a simple starting point for understanding service health.
3. Traffic tells us about demand.
4. Errors tell us about failures.
5. Latency tells us about response time.
6. Saturation tells us about remaining capacity.
7. Do not rely only on averages for latency; percentiles such as p95 and p99 are often more useful.
8. Golden Signals should be combined with metrics, logs, and traces for deeper troubleshooting.
9. Golden Signals are about **service health**, while infrastructure metrics help explain the underlying cause.
10. The goal is not to monitor everything. The goal is to quickly identify whether users are experiencing a problem and where to investigate next.

---

## References

* Google SRE Book — Monitoring Distributed Systems
  https://sre.google/sre-book/monitoring-distributed-systems/

* Google SRE Book — Service Level Objectives
  https://sre.google/sre-book/service-level-objectives/

* Google Cloud — Four Golden Signals
  https://sre.google/sre-book/monitoring-distributed-systems/#the-four-golden-signals

* Google Cloud — Monitoring and Observability
  https://cloud.google.com/stackdriver/docs/solutions/slo-monitoring
