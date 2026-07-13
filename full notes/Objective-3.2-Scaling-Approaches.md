# Objective 3.2 — Scaling Approaches

## SECTION 1 — Exam Objectives

- **Objective 3.2:** *Given a scenario, configure appropriate scaling approaches.*
- **The approaches and types you must know:**
  - **Approaches — Triggered** — scaling kicked off by a condition:
    - **Trending** — predict future load from historical patterns and pre-scale.
    - **Load** — react to current utilization (CPU, requests, queue depth) crossing a threshold.
    - **Event** — react to a discrete event (a queue message, a webhook, a scheduled job).
  - **Approaches — Scheduled** — scale at fixed times from known cycles (business hours, month-end).
  - **Approaches — Manual** — a human changes capacity through the console or API.
  - **Types — Horizontal** — add/remove instances (scale out/in); improves availability.
  - **Types — Vertical** — make instances bigger/smaller (scale up/down); has a ceiling.
- **Why it matters:** Scaling is how a cloud system matches capacity to demand — too little and users see errors and slowness, too much and you waste money. The exam wants the right approach+type combo for a scenario.
- **How CompTIA tests it:** Scenario-driven. Given a workload, pick among triggered (trending/load/event), scheduled, and manual approaches, and the horizontal/vertical types, weighing cost vs. performance vs. availability.

## SECTION 2 — EXAM KNOWLEDGE

### Triggered — Trending
- **Definition:** Trending (predictive) scaling analyzes historical usage patterns to forecast future demand and adjusts capacity *before* the load arrives, avoiding the reaction lag inherent in reactive scaling.
- **Why it matters:** Valuable for long-provisioning workloads (large VMs, warm caches, data clusters) and recurring spikes; reduces both over- and under-provisioning. Caveat: needs enough history; unusual events (a viral moment) won't be in the trend.
- **Analogy:** Pre-heating the oven before the dinner rush — capacity is ready before demand, not after a threshold breach.
- **Example:** Traffic grows 20% every Monday at 9 a.m.; a trending policy scales out Sunday night so users never hit a capacity wall. On AWS this is predictive scaling (ML) on Auto Scaling, run alongside step/target tracking as a safety net.

### Triggered — Load
- **Definition:** Load-based (reactive) scaling adjusts capacity when real-time utilization metrics cross defined thresholds — the most common and intuitive approach.
- **Why it matters:** Excellent for unpredictable, spiky traffic, but suffers reaction lag (boot/warm-up window). Components: target metric, threshold/breach duration, scaling adjustment, cooldown. The exam frequently tests cooldowns — without them a system flaps (scale out then in).
- **Analogy:** Opening more checkout lanes only after the queue is already long — easy to set up, but customers wait during the lag.
- **Example:** When avg CPU > 70% for 5 min, add 2 instances; when < 30%, remove 2. On AWS this is target tracking / step scaling on an Auto Scaling Group.

### Triggered — Event
- **Definition:** Event-based scaling triggers a capacity change in response to a discrete, often external occurrence (a queue message, a dropped file, a webhook) rather than a continuous metric threshold.
- **Why it matters:** Decoupled and precise — capacity follows the actual work item, ideal for asynchronous/batch/bursty workloads with no steady "load." Can scale to zero when idle. Challenge: reliable event source and keeping pace with arrival.
- **Analogy:** A barista starts a new drink only when an order ticket prints — one task per event, idle when no tickets arrive.
- **Example:** Each uploaded video is a queue message that launches a transcoding task (serverless, scales to zero when idle). On AWS: EventBridge/Lambda or queue-depth alarms driving Auto Scaling.

### Approaches — Scheduled
- **Definition:** Scheduled scaling changes capacity at predetermined times via a calendar/cron plan, based on demand predictable from the clock rather than from metrics.
- **Why it matters:** Simple and predictable; avoids both reaction lag (load) and forecast error (trending) because the pattern is known by policy. Often combined with triggered policies as a baseline. Pitfalls: a stale schedule and time-zone mistakes across regions.
- **Analogy:** Setting the sprinklers to run at 6 a.m. every day — capacity is present exactly when needed, no metric required.
- **Example:** Scale out to 20 instances weekdays at 8 a.m., back to 5 at 8 p.m. On AWS: scheduled actions on an ASG (cron expression + desired capacity).

### Approaches — Manual
- **Definition:** Manual scaling is a human explicitly changing capacity through a console, CLI, API, or IaC change — no automation decides.
- **Why it matters:** Full control and simplest; good for one-off, governed, or low-frequency changes. Trade-off: zero reaction-lag control — unexpected spikes go unhandled until a human acts. Can still target either type (add instances = horizontal, resize = vertical).
- **Analogy:** Turning the thermostat up by hand when you feel cold — direct, but nobody adjusts it while you're asleep.
- **Example:** The ops team resizes an EC2 instance or sets ASG desired capacity for a known event. On AWS: manual desired capacity on an ASG or stop/start resize (vertical).

### Types — Horizontal
- **Definition:** Horizontal scaling (out/in) adds or removes identical resources — instances, containers, or nodes — to distribute load across more units rather than making one unit bigger.
- **Why it matters:** The cloud-native default; improves both capacity and availability (survives instance failure, spans AZs). Requires a stateless / externalized-state app. Effectively unbounded; pairs with triggered/scheduled via an ASG or K8s replica set. Cost scales linearly with count.
- **Analogy:** Adding more toll booths instead of widening one — throughput grows and one booth closing doesn't stop traffic.
- **Example:** A stateless web tier behind a load balancer auto-scales out on CPU; scale-in drains connections (a frequently tested concept). On AWS: ASG, HPA, or serverless concurrency.

### Types — Vertical
- **Definition:** Vertical scaling (up/down) changes the size of an existing resource — typically resizing a VM to a larger/smaller instance type — rather than the count.
- **Why it matters:** Simplest when an app can't be made stateless or distributed (legacy monoliths, single-writer DBs, per-socket licensing). Hard limits: a maximum instance size (ceiling) and a stop/start reboot (downtime). Sacrifices availability and elasticity versus horizontal.
- **Analogy:** Replacing one truck with a bigger truck — more capacity, but you must park it (downtime) and there's a biggest truck you can't exceed.
- **Example:** An unshardable DB bottleneck is resized from 4xlarge to 12xlarge over a maintenance window, accepting a brief reboot for headroom. On AWS: change EC2 instance type or resize an RDS DB instance.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Retail site, spiky traffic (Horizontal + Load):** An Auto Scaling Group keeps 4 web servers at baseline and adds 2 whenever average CPU > 70% for 5 minutes, removing them when < 30%. A flash-sale spike scales to 20 instances automatically; cooldowns prevent flapping. Users never see errors.
- **Monthly payroll batch (Scheduled):** A finance cluster is scaled to 50 nodes at 1 a.m. on the 1st of each month to run payroll, then scheduled back to 0 at 6 a.m. Cost is near-zero the rest of the month, and capacity is guaranteed exactly when needed.
- **Video transcoding queue (Event):** Each uploaded video appears as a message in a queue; a worker pool scales out one task per message (serverless functions), then scales to zero when the queue empties. No idle servers, no metric-threshold guessing.
- **Predictable morning ramp (Trending):** A SaaS app learns traffic rises every weekday at 8 a.m. Predictive scaling provisions extra capacity by 7:45 a.m. so the first users of the day experience no lag, without waiting for a CPU breach.
- **Legacy monolith database (Vertical):** A single primary DB cannot be sharded; during quarter-end reporting it is resized from a 4xlarge to a 12xlarge instance (scale up) over a planned maintenance window, accepting a brief reboot to gain headroom.

## SECTION 4 — COMPARISON TABLES

| Approach | Trigger | Best when | Lag | Exam keyword |
|---|---|---|---|---|
| Triggered — Trending | Forecast from history | Recurring, predictable growth | None (pre-scales) | predictive |
| Triggered — Load | Metric threshold | Unpredictable, spiky | Reaction lag | CPU > 70% |
| Triggered — Event | Discrete event/queue | Async/batch, bursty | Near-zero | message arrives |
| Scheduled | Clock/cron | Known business cycle | None (planned) | business hours |
| Manual | Human action | Governed/one-off | Human-dependent | console change |

| Type | What changes | Ceiling | Downtime | Availability |
|---|---|---|---|---|
| Horizontal (out/in) | Instance count | High (quota/wallet) | None (with LB) | Improves |
| Vertical (up/down) | Instance size | Hard max size | Usually reboot | Reduces |

| Concept | Purpose | Pitfall if missing |
|---|---|---|
| Cooldown | Stop scaling thrash | Flapping / oscillation |
| Connection draining | Graceful scale-in | Dropped user sessions |
| Stateless app | Enable horizontal | Sessions break on scale-out |
| Desired capacity | Target instance count | Under/over provisioning |

| Scaling driver | Example metric | Pairs with type |
|---|---|---|
| CPU utilization | % CPU avg | Horizontal |
| Queue depth | Messages waiting | Horizontal (event) |
| Schedule | Cron timestamp | Either |
| Forecast | Historical pattern | Horizontal |

| Cost behavior | Horizontal | Vertical |
|---|---|---|
| Scaling unit | +1 instance | Bigger instance |
| Cost curve | Linear with count | Step at resize |
| Idle cost | Scale to zero possible | Always on (min size) |

## SECTION 5 — AWS PROVIDER MAPPING

AWS-ONLY mapping for Objective 3.2 (scaling approaches):

- **AWS Auto Scaling** (the service) and **Auto Scaling Groups (ASG)** — the core mechanism for horizontal scaling on EC2. An ASG maintains a desired count of instances, can scale out/in based on:
  - **Load** — target tracking (e.g., keep CPU at 50%) and step scaling policies reacting to CloudWatch metric alarms.
  - **Trending** — predictive scaling (machine-learning forecast of demand) that provisions ahead of time, combinable with target tracking as a safety net.
  - **Scheduled** — scheduled actions with cron expressions that set min/max/desired capacity at known times (e.g., scale out at 8 a.m.).
  - **Manual** — an operator sets desired capacity directly in the console/CLI/API.
  ASGs launch instances via a launch template/config across multiple AZs for availability, use **cooldown** periods to prevent thrashing, and integrate with Elastic Load Balancing (connection draining on scale-in) and health checks (unhealthy instances are replaced — self-healing). This single construct covers every approach (trending/load/event/scheduled/manual) and the horizontal type.

- **Vertical scaling on AWS** is performed by **changing the instance type** of an EC2 instance (stop, change type, start) or resizing an RDS DB instance — there is no "auto vertical scaler"; it is manual/scheduled and incurs reboot downtime, mapping to the Vertical *type*.

Event-driven horizontal scaling also appears via **AWS Lambda** (scales concurrency per invocation, to zero when idle) and **Amazon ECS/EKS** with queue-depth or custom metrics, but the exam's primary AWS scaling answer is the **Auto Scaling Group / ASG**.

## SECTION 6 — PRACTICE QUESTIONS

1. A stateless web app faces unpredictable traffic and must stay highly available. Best scaling choice?
    A. Vertical only
    B. Horizontal + load-triggered
    C. Manual resize nightly
    D. Scheduled to fixed count

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A stateless app with spiky demand and an HA requirement is the textbook case for horizontal scale-out driven by load (CPU/request) triggers.
> **Why A is wrong:** Vertical only hits a hard instance-size ceiling and sacrifices availability.
> **Why C is wrong:** Manual nightly resize cannot react to surprise spikes and adds reaction lag.
> **Why D is wrong:** A fixed scheduled count cannot absorb unpredictable spikes.

2. Which approach scales capacity BEFORE the load arrives using history?
    A. Load
    B. Event
    C. Trending (predictive)
    D. Manual

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Trending/predictive scaling forecasts from historical patterns and provisions ahead of demand, eliminating reaction lag.
> **Why A is wrong:** Load scaling reacts after a metric threshold is crossed, so it lags.
> **Why B is wrong:** Event scaling fires on a discrete occurrence, not a forecast.
> **Why D is wrong:** Manual scaling waits for a human to act, not a prediction.

3. Scaling based on "average CPU > 70% for 5 minutes" is:
    A. Scheduled
    B. Trending
    C. Load-triggered
    D. Manual

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Reacting to a real-time metric threshold (CPU breach for a duration) is load-triggered (reactive) scaling.
> **Why A is wrong:** Scheduled scaling uses clock/cron, not a live metric.
> **Why B is wrong:** Trending forecasts from history; it does not wait for a threshold breach.
> **Why D is wrong:** Manual requires a human action, not an automatic threshold.

4. A video upload triggers one transcoding task per file, scaling to zero when idle. This is:
    A. Trending
    B. Event-triggered
    C. Scheduled
    D. Vertical

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Capacity follows a discrete event (the upload/queue message) and scales to zero when idle — classic event-driven scaling.
> **Why A is wrong:** Trending needs historical patterns, not per-file events.
> **Why C is wrong:** Scheduled runs on a clock, not on upload arrival.
> **Why D is wrong:** Vertical changes instance size, not task count, and cannot scale to zero meaningfully.

5. "Scale to 20 instances weekdays at 8 a.m., back to 5 at 8 p.m." is:
    A. Load
    B. Event
    C. Scheduled
    D. Trending

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** A known, clock-based cycle (cron) that sets capacity at fixed times is scheduled scaling.
> **Why A is wrong:** Load reacts to utilization, not a fixed time.
> **Why B is wrong:** Event reacts to discrete occurrences, not a daily clock.
> **Why D is wrong:** Trending forecasts from history; here the pattern is simply known by policy.

6. The main risk of manual scaling is:
    A. Always costs more
    B. Slow response to unexpected demand
    C. Requires stateless apps
    D. Causes reboot downtime

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A human must act, so unexpected spikes go unhandled until someone intervenes — reaction lag by definition.
> **Why A is wrong:** Manual can be cheap; it is not defined by always costing more.
> **Why C is wrong:** Manual can target either horizontal or vertical; it does not require statelessness.
> **Why D is wrong:** Reboot downtime is a vertical-scaling trait, not manual scaling generally.

7. Horizontal scaling requires the app to be:
    A. Stateful
    B. Monolithic
    C. Stateless (or externalized state)
    D. Single-instance

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Any instance must serve any request, so session/state must be externalized (DB/cache) for scale-out to work.
> **Why A is wrong:** Stateful apps break when requests land on different instances after scale-out.
> **Why B is wrong:** Monoliths can run, but without externalized state they do not scale horizontally cleanly.
> **Why D is wrong:** Single-instance is the opposite of horizontal (multiple-instance) scaling.

8. Vertical scaling's hard limitation is:
    A. It cannot reduce cost
    B. A maximum instance size (ceiling)
    C. It always scales to zero
    D. It needs a load balancer

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Every instance family has a largest size, so vertical scaling hits an absolute ceiling and cannot grow beyond it.
> **Why A is wrong:** Vertical can reduce cost by right-sizing down, not just up.
> **Why C is wrong:** Vertical resizes an existing instance; it does not scale to zero.
> **Why D is wrong:** A load balancer is for horizontal fleets, not required for a single resized instance.

9. Connection draining is important during:
    A. Scale-out
    B. Graceful scale-in
    C. Vertical resize
    D. Scheduled action

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Draining lets in-flight requests on an instance finish before it is removed during scale-in, avoiding dropped sessions.
> **Why A is wrong:** Scale-out adds instances; no existing sessions need draining.
> **Why C is wrong:** Vertical resize is a stop/start of one instance, not a fleet removal.
> **Why D is wrong:** Scheduled actions may scale in, but draining itself is the graceful-removal mechanism, not the schedule.

10. Cooldown periods prevent:
    A. Scaling out
    B. Flapping / rapid oscillation
    C. Scheduled actions
    D. Vertical scaling

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A cooldown blocks new scaling actions briefly after one, stopping repeated scale-out/in from brief spikes (flapping).
> **Why A is wrong:** Cooldowns do not block scaling out; they pause the next action temporarily.
> **Why C is wrong:** Scheduled actions run on cron regardless of cooldown.
> **Why D is wrong:** Cooldown is an ASG (horizontal) concept, not vertical scaling.

11. An ASG replacing an unhealthy instance is an example of:
    A. Trending
    B. Self-healing (horizontal)
    C. Vertical scaling
    D. Manual scaling

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Health-check replacement brings capacity back to desired count automatically — self-healing horizontal capacity.
> **Why A is wrong:** Trending is predictive pre-scaling, not health replacement.
> **Why C is wrong:** Replacing an instance keeps the same size; it is not a vertical resize.
> **Why D is wrong:** The ASG acts automatically; no human is required.

12. Which scaling type improves availability by adding redundancy?
    A. Vertical
    B. Horizontal
    C. Manual only
    D. Scheduled only

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Adding instances across AZs removes single-instance failure points, so horizontal scaling improves availability.
> **Why A is wrong:** Vertical resizing one instance usually causes reboot downtime and reduces availability.
> **Why C is wrong:** Manual is an approach, not a type, and does not inherently add redundancy.
> **Why D is wrong:** Scheduled is an approach, not a type, and may set a single fixed count.

13. Predictive scaling on AWS is a form of:
    A. Scheduled
    B. Event
    C. Trending (triggered)
    D. Manual

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** AWS predictive scaling uses ML to forecast demand and pre-provision — that is the trending/forecast triggered approach.
> **Why A is wrong:** Scheduled uses cron; predictive uses a learned demand forecast.
> **Why B is wrong:** Event scaling reacts to discrete occurrences, not forecasts.
> **Why D is wrong:** Predictive scaling runs automatically, not via human action.

14. Resizing an EC2 instance to a larger type usually requires:
    A. No downtime
    B. A stop/start (reboot)
    C. A load balancer change
    D. Adding instances

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Changing an EC2 instance type means stop, change type, start — a reboot/downtime window.
> **Why A is wrong:** You cannot change hardware type on a running instance; a stop/start is required.
> **Why C is wrong:** The instance stays behind the same LB; no LB change is needed.
> **Why D is wrong:** Adding instances is horizontal scaling, not a vertical resize.

15. Event-based scaling is ideal for:
    A. Steady 24/7 traffic
    B. Asynchronous/batch bursty workloads
    C. Single legacy DB
    D. Fixed capacity

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Capacity follows each work item (queue message/webhook), so event scaling suits async, batch, bursty work and scales to zero.
> **Why A is wrong:** Steady traffic is better served by load or scheduled scaling.
> **Why C is wrong:** A single legacy DB is a vertical-scaling or fixed-capacity case.
> **Why D is wrong:** Fixed capacity contradicts event scaling, which varies with arrivals.

16. Combining a schedule (baseline) with load rules is done to:
    A. Eliminate cooldowns
    B. Handle predictable base + surprise spikes
    C. Force vertical scaling
    D. Disable alarms

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** The schedule sets a known baseline capacity; load rules catch unpredictable spikes on top of it.
> **Why A is wrong:** Cooldowns remain necessary to prevent flapping regardless of scheduling.
> **Why C is wrong:** This combination is horizontal (count-based), not vertical.
> **Why D is wrong:** Alarms still drive the load rules; they are not disabled.

17. A metric like "queue depth" most naturally drives:
    A. Vertical scaling
    B. Event or load-triggered horizontal
    C. Scheduled scaling
    D. Manual only

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Queue length is a real-time signal of pending work, so it naturally triggers horizontal scale-out (event/load) to drain the backlog.
> **Why A is wrong:** Queue depth reflects work volume, not instance size needs.
> **Why C is wrong:** Scheduled ignores live queue length and runs on a clock.
> **Why D is wrong:** Manual would not react promptly to queue fluctuations.

18. The biggest availability downside of vertical scaling is:
    A. It scales to zero
    B. Reboot downtime during resize
    C. It needs stateless apps
    D. It adds load balancers

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Resizing usually stops the instance, causing a reboot/downtime window and reducing availability.
> **Why A is wrong:** Vertical does not scale to zero; that is event/serverless behavior.
> **Why C is wrong:** Vertical is often chosen precisely because an app is NOT stateless.
> **Why D is wrong:** A single resized instance typically sits behind an existing LB; adding one is not the downside.

19. On AWS, the primary construct for horizontal EC2 scaling is:
    A. Lambda
    B. Auto Scaling Group (ASG)
    C. CloudTrail
    D. X-Ray

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** An ASG maintains a desired instance count and applies load/trending/scheduled/manual policies plus health replacement.
> **Why A is wrong:** Lambda scales concurrency per invocation, not EC2 instance count.
> **Why C is wrong:** CloudTrail is audit logging, unrelated to scaling.
> **Why D is wrong:** X-Ray is tracing, unrelated to scaling.

20. For a quarter-end reporting DB that can't be sharded, the right move is:
    A. Horizontal ASG
    B. Vertical scale-up (bigger instance)
    C. Event scaling
    D. Scheduled to zero

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** An unshardable single-writer DB bottleneck is relieved by resizing to a larger instance (vertical scale-up) over a maintenance window.
> **Why A is wrong:** Horizontal ASGs cannot shard a single primary DB's state.
> **Why C is wrong:** Event scaling adds task count, not DB capacity, and the DB is not event-driven.
> **Why D is wrong:** Scaling a production DB to zero would destroy availability.
