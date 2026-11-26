# DevOps in Practice

**Build, ship, and operate a service from its first commit to its first incident.**

## Who this course is for

- **Junior developers moving into DevOps** should be comfortable editing a small program, running a test, and making a Git commit. Start with the guided labs. They introduce Linux, networking, containers, and infrastructure as code through problems you can reproduce on your laptop.
- **Junior DevOps engineers building toward senior responsibilities** can use each lab's success criterion as a checkpoint. If you can already demonstrate it, move straight to the engineering challenge. These challenges ask you to weigh trade-offs, explain operational risk, and leave evidence a teammate can act on.

## Class project and setup

You join the team responsible for a small Python orders API backed by PostgreSQL. A customer can create an order and retrieve it by ID. That transaction becomes your test throughout the course. By the end, a teammate should be able to release the API, recognize when customers are affected, and recover their orders using your repository and runbook.

You'll need Git, a terminal, a container runtime compatible with Docker Compose, and a GitHub account for the CI and image registry labs. The course walks through the remaining tool setup. Infrastructure and deployment labs run locally, including the Kubernetes cluster. No paid cloud account is required.

Planned course resources include a starter API, sample orders, fault scenarios, and a runbook template. Use a dedicated practice repository for the delivery exercises.

**Suggested pace** is one or two sections per week after the introductory lecture. Each numbered section includes three short lectures and a practical exercise. Allow 30–60 minutes for a typical lab and about two hours for the final exercise.

## What you'll be able to do

- Trace a failed request from the client to the database and identify the component responsible.
- Build a CI pipeline that blocks a faulty change and publishes a traceable container image.
- Review an infrastructure plan, identify destructive changes, and reconcile configuration drift.
- Deploy a tested image to Kubernetes, verify it through a customer transaction, and roll back a failed release.
- Define a service-level objective, measure customer impact, and use that evidence during an incident.
- Restore data into a clean environment and hand operational ownership to another engineer.

## Curriculum

### Introductory lecture — What does a DevOps team do?

**6 minutes.** By the end of this lesson, you can explain DevOps using one customer order and choose where to begin your practice.

- **Minute 0–1 · Meet the customer.** Watch a shopper place an order and reopen it. Tomorrow the team will change how order IDs are generated. What would convince you that today's customer can still find their order after the release?
- **Minutes 1–4 · Meet the team.** Follow a developer and an on-call engineer as they turn that question into a test, run it before release, and check for lookup errors afterward. This is DevOps in practice. The people who build and run the service share responsibility for how it works in production, and what they learn there shapes the next change.
- **Minutes 4–6 · Choose your starting point.** Pause and write down one check you would run and what result you would expect. If you need help turning your idea into a test, start with the guided lab in section 1. If you already run a similar check, take the engineering challenge and identify a failure your check could miss.

### 1. Follow an order from commit to customer

**Lectures** · Trace the request and the delivery path · Identify who owns each handoff · Turn a user story into a smoke test

**Guided lab.** Start the supplied service with the course launcher. Create an order, save its ID, and retrieve it. Draw the path the request took and record a repeatable smoke test that fails if the saved order cannot be read back.

**Engineering challenge.** The team currently calls a deployment successful when the process starts. Show one way the customer transaction can still fail and propose a release check that would catch it.

### 2. Diagnose a service on Linux

**Lectures** · Inspect processes, permissions, and logs · Follow DNS, TCP, and HTTP in sequence · Automate a diagnostic check with shell exit codes

**Guided lab.** Diagnose two seeded faults, a connection to the wrong port and an invalid database hostname. For each fault, capture the observation that pinpoints the failure, apply the fix, and rerun the smoke test.

**Engineering challenge.** Write the first three checks for an on-call teammate who sees a connection error. Explain how the result of each check determines the next step.

### 3. Package the API and its dependencies

**Lectures** · Build a Docker image from a Dockerfile · Connect services with Compose · Separate configuration and persistent data from the image

**Guided lab.** Write a Dockerfile and Compose configuration for the API and PostgreSQL. Run the API as a non-root user, create an order, recreate the containers, and verify the order survives.

**Engineering challenge.** Change one line of application code and measure a rebuild. Reorder the build steps to reuse dependency layers, then show which steps ran again and why.

### 4. Make GitHub Actions decide what can ship

**Lectures** · Choose checks for pull requests · Run integration tests against PostgreSQL · Publish a tested image and trace it to a commit

**Guided lab.** Make a failing integration test block a pull request through a required check. Fix it, merge the change, and have the release workflow test and publish the resulting image to GitHub Container Registry. Record the image digest beside the source commit.

**Engineering challenge.** Review which jobs can write to the registry. Demonstrate that pull requests can run the tests and only the publishing job on the release path receives write access.

### 5. Manage infrastructure changes with Terraform

**Lectures** · Describe resources and dependencies · Read a plan before applying it · Interpret state, drift, and resource replacement

**Guided lab.** Use Terraform's Docker provider to create a separate local database for recovery drills. Change its configuration outside Terraform, inspect the next plan, and reconcile the difference. A final plan should report no changes.

**Engineering challenge.** Review a proposed volume replacement while the volume holds an order. Identify the data at risk and write the backup and verification steps you would require before approving the change.

### 6. Deploy and troubleshoot on Kubernetes

**Lectures** · Connect Pods, Deployments, and Services · Configure readiness, liveness, and resource requests · Read events and rollout status

**Guided lab.** Deploy the lab database and the published API image to a local kind cluster. Expose the API through a Service and deliberately misconfigure a readiness probe. Use events and endpoint information to explain why the new Pod receives no traffic. Correct the probe and complete the order transaction.

**Engineering challenge.** Decide how readiness and liveness should behave during a brief database outage. Test your configuration and use the restart count and request results to defend your choice.

### 7. Release a change and recover from a bad one

**Lectures** · Promote an image by digest · Define checks for a rolling update · Separate application rollback from database recovery

**Guided lab.** From your workstation, deploy a new image digest to the cluster. Then release a version whose health endpoint passes but whose order lookup fails. Use the transaction smoke test to detect the regression, roll back, and verify that an order created before the release is readable again.

**Engineering challenge.** Plan a database column rename while two API versions may serve traffic. Specify the deployment order, the last safe point for an application rollback, and the evidence needed before removing the old column.

### 8. Measure reliability from the customer's perspective

**Lectures** · Correlate requests with structured logs · Measure request rate, errors, and latency with Prometheus · Set a service-level objective and interpret an error budget

**Guided lab.** Generate order traffic, display success rate and latency in Grafana, and inject lookup failures. For a target of 99% successful lookups, calculate how many failures the observed request count allows. Compare that allowance with the failures you measured.

**Engineering challenge.** Configure an alert for sustained customer impact. Replay a brief spike and a persistent failure, then explain the trade-off between detection time and unnecessary pages.

### 9. Handle an incident and rehearse a restore

**Lectures** · Triage and communicate customer impact · Restore a backup and verify data integrity · Write a blameless incident review

**Guided lab.** Investigate a burst of failed transactions, post a simulated status update, and verify recovery with the smoke test. In a separate recovery drill, restore a PostgreSQL backup into the clean database from section 5. Check known order IDs and measure how long the restore took.

**Engineering challenge.** Given a 15-minute recovery time objective and a 5-minute recovery point objective, use the drill timings and backup timestamps to judge whether the team could meet each one. Assign an owner and a measurable success criterion to one corrective action.

### 10. Design the first cloud deployment

**Lectures** · Trace traffic through a load balancer and private network · Grant workload identities access to secrets · Balance availability, operating effort, and cost

**Guided lab.** Using an architecture worksheet, map the local service to a cloud deployment with a public HTTPS endpoint, a private application, and a managed database. Mark which connections are allowed, where the database credential lives, and which identity can read it. Trace an order through the diagram and check each network and permission boundary.

**Engineering challenge.** The team has two engineers on call and can tolerate a single application instance failing. Compare two replicas on one host with replicas across failure zones. Explain what customers experience if a host or zone fails, what the database still depends on, and which additional expense you would approve first.

### 11. Capstone project | Own a release on call

**Lectures** · Prepare a release handoff · Make a go-or-rollback decision · Present evidence in an operational review

Ship a change to order validation through the pipeline. During the rollout, work through an unseen fault in either the application release or its runtime configuration. Keep a timeline, send a simulated stakeholder update, recover the customer transaction, and give another engineer enough information to repeat the release.

| Assessment | Evidence of completion |
| --- | --- |
| Release integrity | Link a passing workflow run, source commit, and deployed image digest. Show the check that would block a failing change. |
| Diagnosis and decision | Use an observed customer symptom and a log, metric, or event to justify continuing, correcting configuration, or rolling back. |
| Recovery | Pass the create-and-retrieve test and verify an order created before the incident. Record customer impact and time to recovery. |
| Handoff | Deliver a runbook with release checks and recovery steps. Include an incident review with one proposed improvement, its owner, and a way to verify it worked. |

**Senior-level extension.** The team can fund one improvement next sprint. Use evidence from the release and recovery drills to choose between faster rollback, more frequent backups, or better detection. Submit a one-page decision record with your rationale, estimated effort, and the measurement that would make you revisit the decision.
