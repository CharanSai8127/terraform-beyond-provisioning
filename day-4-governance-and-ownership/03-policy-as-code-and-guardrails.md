# Policy as Code and Guardrails

## From Infrastructure as Code to Policy as Code

Infrastructure as Code transformed the way organizations provision infrastructure.

Instead of manually creating resources through cloud consoles, engineers began defining infrastructure requirements as code.

Terraform allows teams to describe:

* Networks
* Databases
* Clusters
* Storage
* Security Resources

using declarative configuration.

Terraform answers the question:

> What infrastructure should exist?

As organizations scale, another question emerges:

> What infrastructure should be allowed to exist?

This is where Policy as Code becomes necessary.

---

## What Is Policy as Code?

Policy as Code is the practice of defining governance, security, compliance, and operational standards using code.

Just as Infrastructure as Code defines infrastructure requirements, Policy as Code defines infrastructure constraints.

Infrastructure as Code specifies:

```text
Desired Infrastructure
```

Policy as Code specifies:

```text
Allowed Infrastructure
```

Together they create:

```text
Infrastructure as Code
            +
Policy as Code
            ↓
Governed Infrastructure
```

---

## Why Policy as Code Exists

Governance becomes increasingly difficult as organizations scale.

Consider a platform supporting:

* Hundreds of engineers
* Multiple cloud accounts
* Thousands of resources
* Multiple environments

Without automation, governance relies heavily on manual reviews.

Reviewers must verify:

* Encryption settings
* Backup configurations
* Resource tags
* Security policies
* Network restrictions
* Compliance requirements

This process quickly becomes unmanageable.

Policy as Code automates governance.

Instead of relying entirely on human review, policies are evaluated automatically before infrastructure changes are applied.

---

## Policy as Code as Preventive Governance

Traditional governance often operates after resources are created.

Example:

```text
Resource Created
        ↓
Audit Performed
        ↓
Violation Found
```

The problem already exists.

Policy as Code shifts governance earlier in the lifecycle.

```text
Terraform Plan
        ↓
Policy Evaluation
        ↓
Pass / Fail
        ↓
Terraform Apply
```

Violations are detected before infrastructure is provisioned.

This approach significantly reduces operational risk.

---

## Defining Organizational Standards

Every organization develops infrastructure standards.

Examples include:

```text
All Resources Must Be Tagged

All Storage Must Be Encrypted

Production Databases Must Have Backups

Only Approved Regions May Be Used

Public Access Must Be Restricted

Resource Limits Must Be Defined
```

Without Policy as Code:

```text
Engineer
        ↓
Creates Resource
        ↓
Reviewer Checks Standards
```

With Policy as Code:

```text
Engineer
        ↓
Creates Resource
        ↓
Policy Validation
        ↓
Automatic Enforcement
```

Governance becomes scalable.

---

## Infrastructure Behavior as Code

Policy as Code does not create resources.

Instead, it defines how resources should behave.

Terraform may define:

```text
Create S3 Bucket
```

Policy defines:

```text
Encryption Enabled

Versioning Enabled

Public Access Disabled
```

Terraform defines the resource.

Policy defines the acceptable configuration of the resource.

This ensures governance is applied consistently across the platform.

---

## AWS Governance Controls

Cloud providers offer native governance mechanisms.

### Service Control Policies (SCPs)

Service Control Policies are applied through AWS Organizations.

They define the maximum permissions available within accounts.

Examples:

```text
Deny Resource Creation
Outside Approved Regions

Deny Public S3 Buckets

Restrict Administrative Actions
```

SCPs operate as preventive controls.

---

### AWS Config

AWS Config continuously evaluates deployed resources.

Examples:

```text
Verify Encryption

Verify Resource Tags

Verify Security Group Rules

Verify Backup Configuration
```

AWS Config acts as a detective control.

It identifies violations after resources exist.

---

### IAM Policies

IAM policies define:

```text
Who Can Perform Actions
```

Examples:

```text
Create Resources

Modify Resources

Delete Resources

Access Resources
```

IAM establishes governance through access control.

---

## Kubernetes Governance Controls

The same governance principles apply inside Kubernetes clusters.

---

### Open Policy Agent (OPA)

OPA evaluates requests against organizational policies.

Examples:

```text
Deny Privileged Containers

Deny Host Network Access

Require Labels

Require Security Standards
```

Requests violating policy are rejected.

---

### Kyverno

Kyverno is a Kubernetes-native policy engine.

Examples:

```text
Require Resource Limits

Require Labels

Require Network Policies

Require Security Contexts
```

Kyverno can:

```text
Validate

Mutate

Generate
```

resources automatically.

This allows governance to be enforced directly inside the cluster.

---

## Guardrails Versus Restrictions

A common misconception is that governance exists to restrict engineers.

In reality, governance should provide guardrails.

Restrictions block progress.

Guardrails enable safe progress.

Without guardrails:

```text
Unlimited Freedom
        ↓
Operational Chaos
```

With guardrails:

```text
Controlled Freedom
        ↓
Safe Self-Service
```

The objective is to allow teams to move quickly while maintaining security, consistency, and operational reliability.

---

## Policy as Code and Platform Engineering

Platform Engineering depends heavily on Policy as Code.

Modern platforms provide:

* Self-service infrastructure
* Standardized modules
* Golden paths
* Automated governance

Policy as Code ensures self-service remains safe.

Without policies:

```text
Self-Service
        ↓
Infrastructure Drift
```

With policies:

```text
Self-Service
        ↓
Governed Infrastructure
```

This allows organizations to scale infrastructure consumption without sacrificing standards.

---

## Platform Engineering Perspective

Infrastructure as Code solves the provisioning problem.

Policy as Code solves the governance problem.

Terraform answers:

> What should be created?

Policy answers:

> Under what conditions can it be created?

Together they enable organizations to provide infrastructure safely at scale.

---

## Key Takeaway

Infrastructure as Code defines the desired infrastructure.

Policy as Code defines the acceptable infrastructure.

Instead of manually reviewing thousands of resources for compliance and governance, organizations codify standards and automatically enforce them before infrastructure is created.

This transforms governance from a manual process into a scalable, automated capability that protects the platform while enabling safe self-service.

