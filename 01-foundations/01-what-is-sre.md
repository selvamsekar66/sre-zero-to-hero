# What is SRE?

> SRE stands for **Site Reliability Engineering**.

SRE is an engineering discipline that applies software engineering practices to the operation and reliability of software systems.

In simple words:

> **SRE is about making sure a software system is available, reliable, performant, scalable, and recoverable — while reducing repetitive manual work through engineering and automation.**

---

## 1. Why do we need SRE?

Let's start with a simple example.

Imagine you are running an online shopping application.

Customers expect to:

- Open the website
- Log in
- Search for products
- Add products to the cart
- Make payments
- Track their orders

Now imagine the application has thousands or millions of users.

Suddenly:

- A server becomes unavailable
- The database becomes slow
- A deployment introduces an issue
- Traffic increases unexpectedly
- A third-party API stops responding
- Memory usage reaches its limit
- Users start receiving errors

Someone needs to answer questions such as:

> Is the service working?

> How many users are affected?

> How quickly can we detect the problem?

> How quickly can we recover?

> How do we prevent the same problem from happening again?

This is where **Site Reliability Engineering** becomes important.

---

## 2. What does an SRE do?

An SRE applies software engineering practices to operational and reliability problems.

Instead of solving every problem manually, an SRE looks for ways to make systems and processes:

- More reliable
- More observable
- More automated
- More scalable
- Easier to operate
- Easier to recover
- Less dependent on manual intervention

For example:

Instead of manually restarting a failed service every time:

An SRE may look for a better engineering solution:

Service fails
     ↓
Engineer receives alert
     ↓
Engineer investigates
     ↓
Engineer restarts service

However, automation should not be added blindly.

A restart may temporarily restore a service but hide the underlying problem.

Good automation should have appropriate guardrails and should reduce operational effort without repeatedly masking the real issue.

The goal is to:

Engineer a better and more reliable system.
3. SRE in one simple sentence

You can think about SRE like this:

SRE = Software Engineering applied to Reliability and Operations

This is a simple mental model rather than a formal definition.

An SRE does not simply "keep servers running."

An SRE thinks about the reliability of the entire service.

That can include:

Users
  ↓
Application
  ↓
Services
  ↓
APIs
  ↓
Databases
  ↓
Infrastructure
  ↓
Network
  ↓
Cloud / Data Center

A failure at any layer can affect the user.

More importantly:

SRE focuses on the reliability of the service experienced by users, not just the health of individual servers or infrastructure components.

For example:

Server: UP
CPU: Normal
Memory: Normal

But...

Checkout API: Failing
Payments: Failing
Users: Cannot complete purchases

Therefore:

Infrastructure may look healthy
while the service is unhealthy.

This is one of the most important SRE concepts to understand.

4. What does "reliability" mean?

Reliability means that a system performs its intended function consistently when users need it.

For a customer-facing application, reliability can involve:

Availability

Is the service available when users need it?

Performance

Does the service respond within an acceptable amount of time?

Scalability

Can the system handle increasing traffic?

Resilience

Can the system continue operating when something fails?

Recoverability

Can the system recover quickly after a failure?

Durability

Can the system preserve important data correctly despite failures?

So reliability is much more than:

"The server is up."

A server can be up while the application is still failing.

5. SRE is not just monitoring

This is an important distinction.

A common beginner assumption is:

SRE = Monitoring

Monitoring is certainly important, but SRE is much broader.

                 SRE
                  │
      ┌───────────┼───────────┐
      │           │           │
  Monitoring  Automation  Reliability
      │           │           │
 Observability Engineering  Incidents
      │                       │
  Metrics                  Recovery
  Logs                     Postmortems
  Traces                   Improvement

Depending on the organization, an SRE may work with:

Monitoring
Observability
Automation
Cloud infrastructure
Kubernetes
CI/CD
Incident management
Capacity planning
Performance
Security
Disaster recovery
Software engineering
Architecture

The exact responsibilities vary between organizations.

The important point is:

Monitoring is one part of SRE, not the definition of SRE.

6. SRE and software engineering

The word Engineering in SRE is important.

In some environments, operations teams may spend significant time performing repetitive manual tasks.

For example:

Check server
Restart service
Clear disk
Update configuration
Deploy manually
Check logs
Repeat

If an engineer performs the same task repeatedly, an SRE asks:

"Can we solve this problem through engineering?"

That could mean:

Writing a script
Building automation
Improving the architecture
Creating a self-healing mechanism
Improving deployment processes
Adding better observability
Removing the root cause
Building a platform or reusable solution

This type of repetitive operational work is commonly referred to as toil in SRE.

We will explore toil in more detail in a later lesson.

The mindset is:

Don't just keep fixing the same problem. Look for ways to engineer the problem away.

7. SRE and business

Reliability is not only a technical problem.

It is also a business problem.

Imagine a payment application.

If the payment service is unavailable:

Technical failure
       ↓
Payment fails
       ↓
Customer cannot complete purchase
       ↓
Revenue may be affected
       ↓
Customer experience is affected

This is why SRE connects technology with business outcomes.

A good SRE should eventually be able to answer:

"What does this technical problem mean for the customer and the business?"

The goal of SRE is therefore not simply to keep infrastructure running.

It is to provide reliable services that meet user and business needs.

8. What problems does SRE try to solve?

Some common problems include:

Frequent incidents

The same problem happens repeatedly.

SRE looks for ways to eliminate or reduce the underlying cause.

Too much manual work

Engineers spend large amounts of time performing repetitive operational tasks.

SRE looks for opportunities to automate or improve the process.

Poor visibility

Teams don't know what is happening inside their systems.

SRE improves monitoring and observability.

Uncontrolled changes

Changes and deployments can introduce reliability problems.

SRE uses engineering practices to make changes safer and more measurable.

Scaling problems

The system works with 1,000 users but struggles with 100,000.

SRE considers capacity and scalability.

Slow incident recovery

Problems take hours to identify and recover from.

SRE improves detection, response, recovery, and learning from incidents.

9. A simple SRE mindset

Instead of thinking:

"The server is down. Restart it."

Think:

"Why did it fail?"

Then:

"How did we detect it?"

Then:

"Why wasn't it detected earlier?"

Then:

"Can we recover safely?"

Then:

"How can we prevent this from happening again?"

Finally:

"What did we learn from this incident?"

This is the shift from reactive operations to engineering reliability.

The goal is not only to restore the service.

The goal is to learn, improve, and reduce the chance or impact of future failures.

10. A simple real-world example

Consider a web application.

                    USERS
                      |
                      v
                Load Balancer
                      |
                      v
              Application Servers
                      |
              ┌───────┴───────┐
              |               |
              v               v
           Database         Cache
              |
              v
        External Services

An SRE thinks about questions such as:

Availability

What happens if one application server fails?

Performance

What happens if database response time increases?

Scalability

What happens when traffic increases 10 times?

Observability

How do we know which component is causing the problem?

Incident response

Who is alerted when the service starts failing?

Recovery

Can the system recover safely?

Resilience

What happens if an external dependency becomes unavailable?

Capacity

How much traffic can the system handle?

Dependencies

What happens if a service outside our control becomes unavailable?

For example:

Your Application
      |
      +---- Database
      |
      +---- Payment Provider
      |
      +---- Authentication Provider
      |
      +---- External API

Modern applications depend on many components and external services.

A reliable system must consider not only its own components, but also the behavior and failure of its dependencies.

These questions lead us toward the deeper SRE concepts we will learn later.

11. SRE is a journey, not a tool

SRE is not a specific product or technology.

You don't become an SRE simply by learning:

Splunk
Dynatrace
Prometheus
Grafana
AWS
Kubernetes
Terraform
Python

These are tools and technologies that can help an SRE solve problems.

The important skill is understanding:

What problem are we trying to solve, and why?

Then choosing the appropriate engineering approach and technology.

For example:

Problem
   ↓
Need visibility
   ↓
Observability
   ↓
Metrics + Logs + Traces
   ↓
Choose appropriate tools

A broader way to think about SRE is:

Problem
   ↓
Reliability requirement
   ↓
Engineering approach
   ↓
Architecture
   ↓
Technology / Tool
   ↓
Implementation
   ↓
Measurement

The tool comes after understanding the problem.

12. Key takeaways

After this lesson, you should understand:

SRE stands for Site Reliability Engineering.
SRE applies software engineering practices to reliability and operations.
Reliability is broader than simply keeping a server running.
SRE focuses on the reliability of the service experienced by users.
A system can have healthy infrastructure while the user-facing service is unhealthy.
SRE considers availability, performance, scalability, resilience, recoverability, and durability.
Automation is an important part of SRE, but it should have appropriate guardrails.
Monitoring is important, but SRE is much broader than monitoring.
SRE connects technical reliability with customer and business impact.
Repetitive operational work is commonly referred to as toil.
SRE focuses on solving recurring problems rather than repeatedly performing manual fixes.
Tools are important, but understanding the underlying problem is more important.
13. Beginner check

Before moving to the next lesson, try answering these questions without looking back.

Question 1

What does SRE stand for?

Question 2

Explain SRE in your own words.

Question 3

Why do organizations need SRE?

Question 4

Is SRE the same as monitoring?

Question 5

Why is automation important in SRE?

Question 6

What is the difference between:

"The server is running"

and:

"The service is reliable"
Question 7

Why does reliability matter to the business?

Question 8

A server is healthy, but users cannot complete payments.

Is the service reliable? Why?

Question 9

Why isn't restarting a failed service always a complete solution?

Question 10

What is the difference between monitoring infrastructure and understanding service reliability?

14. What's next?

Now that we understand what SRE is, the next question is:

Why did SRE come into existence?

In the next lesson, we will look at how software operations evolved from traditional IT operations toward DevOps and SRE.

Next: Evolution of SRE

References
AWS — What is Site Reliability Engineering (SRE)?
Dynatrace — Site Reliability Engineering
Google Cloud Skills — SRE learning path