# Infrastructure Drift and Continuous Reconciliation

## Introduction

Infrastructure is rarely modified only through Terraform.

As platforms grow, multiple teams interact with infrastructure.

Engineers troubleshoot production issues.

Security teams implement emergency fixes.

Operations teams respond to incidents.

Application teams modify configurations.

Over time, infrastructure begins to evolve outside Terraform's knowledge.

This phenomenon is known as infrastructure drift.

Drift is one of the most common operational challenges in mature Terraform environments.

The longer infrastructure exists, the more likely drift becomes.

Understanding how to detect, govern, and safely reconcile drift is therefore a critical platform engineering skill.

---

## What Is Infrastructure Drift?

Infrastructure drift occurs when the actual infrastructure no longer matches Terraform's desired state.

In simple terms:

```text
Terraform State
        ≠
Actual Infrastructure
```

Terraform believes the infrastructure exists in one form.

The cloud provider contains something different.

Example:

Terraform manages:

```text
Security Group

Port 443
```

An engineer manually adds:

```text
Port 22
```

through the cloud console.

The infrastructure now becomes:

```text
Terraform
        ↓
Port 443

Cloud Resource
        ↓
Port 443
Port 22
```

Terraform is no longer managing the actual state of the infrastructure.

Drift has occurred.

---

## Why Drift Happens

Drift is not caused by Terraform.

Drift occurs when infrastructure evolves outside Terraform's control.

Common causes include:

### Manual Console Changes

The most common source of drift.

Example:

```text
AWS Console

Azure Portal

GCP Console
```

An engineer manually updates a resource.

Terraform has no knowledge of the modification.

---

### Emergency Production Fixes

Production incidents sometimes require immediate action.

Example:

```text
Firewall Rule Added

Load Balancer Modified

Network Route Updated
```

The emergency fix resolves the outage.

Terraform remains unaware.

---

### Multiple Management Tools

Different tools modifying the same infrastructure.

Example:

```text
Terraform

Cloud Console

Automation Scripts

Cloud Native Services
```

Each tool modifies infrastructure independently.

Consistency becomes difficult.

---

### Platform Evolution

Infrastructure changes continuously.

Examples:

```text
Provider Upgrades

Security Requirements

Compliance Requirements

Network Changes
```

If changes occur outside Terraform workflows, drift appears.

---

## Why Drift Is Dangerous

Drift introduces uncertainty.

Engineers assume:

```text
Git
        ↓
Terraform State
        ↓
Cloud Infrastructure
```

are synchronized.

In reality:

```text
Git
        ↓
Terraform State

Cloud Infrastructure
        ↓
Different
```

The longer drift exists, the larger the gap becomes.

Eventually Terraform may:

```text
Remove Manual Changes

Replace Resources

Overwrite Configurations
```

during the next apply.

Unexpected infrastructure changes often originate from undetected drift.

---

## Detecting Drift

Terraform provides a simple mechanism for drift detection.

```bash
terraform plan
```

Terraform compares:

```text
Terraform State
        ↓
Cloud Provider
```

and identifies differences.

Example:

```text
Manual Change Detected
        ↓
Terraform Plan Shows Update
```

The plan reveals that infrastructure no longer matches Terraform's expectations.

This is the most common method of drift detection.

---

## Terraform Plan as a Drift Detection Tool

Many engineers think:

```bash
terraform plan
```

is simply a preview command.

Platform teams treat it as:

```text
Drift Detection

Risk Assessment

Change Validation
```

The plan reveals:

```text
Resources To Create

Resources To Modify

Resources To Destroy

Resources That Have Drifted
```

before any changes occur.

This visibility is critical for safe operations.

---

## Can GitOps Help?

Yes.

GitOps is one of the most effective strategies for reducing drift.

The principle is simple:

```text
Git
=
Source Of Truth
```

Infrastructure changes should follow:

```text
Git Commit
        ↓
Pull Request
        ↓
Review
        ↓
Pipeline
        ↓
Terraform Apply
```

rather than:

```text
Engineer
        ↓
Cloud Console
        ↓
Manual Change
```

GitOps introduces:

```text
Consistency

Auditability

Reviewability

Visibility
```

which significantly reduces drift.

---

## GitOps Does Not Eliminate Drift

A common misconception is that GitOps completely prevents drift.

It does not.

An engineer with sufficient permissions can still:

```text
Login To Cloud Console
        ↓
Modify Infrastructure
```

Drift remains possible.

GitOps reduces drift.

It does not eliminate drift.

This is why governance remains important.

---

## Preventing Unnecessary Drift

Mature platform teams use multiple layers of protection.

### Restrict Manual Access

Reduce unnecessary write permissions.

Example:

```text
Read Access
        ↓
Most Engineers

Write Access
        ↓
Automation Pipelines
```

Fewer manual changes result in less drift.

---

### GitOps Workflows

Require infrastructure changes to originate from Git.

Example:

```text
Code
        ↓
Review
        ↓
Approval
        ↓
Deployment
```

Every change becomes visible and auditable.

---

### Infrastructure Governance

Apply controls using:

```text
IAM

RBAC

Policy Controls

Service Control Policies
```

to reduce unauthorized modifications.

---

### Continuous Drift Detection

Schedule recurring drift checks.

Example:

```text
GitHub Actions

GitLab CI

Jenkins

Atlantis
```

running:

```bash
terraform plan
```

daily or hourly.

If drift is detected:

```text
Notification
        ↓
Investigation
        ↓
Remediation
```

This allows teams to identify problems before they become incidents.

---

## Correcting Drift Safely

Not all drift should be immediately removed.

The first question should always be:

```text
Why Did The Drift Occur?
```

Understanding the reason determines the appropriate response.

---

### Scenario 1: Unauthorized Change

Example:

```text
Manual Security Group Rule
```

No approval.

No documentation.

No business justification.

Solution:

```text
Terraform Apply
        ↓
Restore Desired State
```

Terraform becomes authoritative again.

---

### Scenario 2: Emergency Production Change

Example:

```text
Firewall Rule Added
```

during an outage.

The change is valid and should remain.

Solution:

```text
Update Terraform Code
        ↓
Commit To Git
        ↓
Apply Through Terraform
```

The change becomes part of the desired state.

This prevents Terraform from removing the fix later.

---

## Continuous Reconciliation

Unlike Kubernetes:

```text
Desired State
        ↓
Controller Loop
        ↓
Continuous Reconciliation
```

Terraform does not continuously reconcile infrastructure.

Terraform operates:

```text
Plan
        ↓
Apply
        ↓
Stop
```

Once apply completes:

```text
Terraform Stops Managing Changes
```

until the next execution.

This is why continuous drift detection becomes important.

It approximates continuous reconciliation through operational processes.

---

## Platform Engineering Perspective

Drift is not a Terraform problem.

Drift is an organizational problem.

It occurs when infrastructure evolves outside controlled workflows.

The objective is not achieving zero drift.

Zero drift is unrealistic.

The objective is:

```text
Drift Detection
        ↓
Drift Visibility
        ↓
Drift Governance
        ↓
Controlled Reconciliation
```

Mature platform teams focus on quickly identifying drift and safely bringing infrastructure back into alignment.

---

## Key Takeaways

* Drift occurs when actual infrastructure differs from Terraform's desired state.
* Manual changes are the most common source of drift.
* Drift becomes more likely as infrastructure ages.
* Terraform plan is a powerful drift detection tool.
* GitOps significantly reduces drift by making Git the source of truth.
* GitOps reduces drift but does not eliminate it.
* Governance controls help prevent unauthorized infrastructure changes.
* Continuous drift detection improves platform reliability.
* Not all drift should be removed immediately.
* Understanding why drift occurred is critical before reconciliation.

## Final Thought

Drift is not caused by Terraform.

Drift is caused when infrastructure evolves outside Terraform's knowledge.

The longer a platform exists, the more opportunities exist for drift to occur.

Successful platform teams therefore focus less on preventing drift entirely and more on detecting, governing, and safely reconciling drift before it becomes an operational problem.

