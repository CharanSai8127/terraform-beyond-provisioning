# The DRY Trap

## Introduction

One of the most widely accepted software engineering principles is DRY (Don't Repeat Yourself).

The objective of DRY is simple. If the same logic exists in multiple places, maintaining it becomes increasingly difficult. A bug fix must be applied everywhere. Security improvements must be implemented repeatedly. Configuration changes must be synchronized across multiple locations.

Terraform follows the same principle through modules.

Instead of defining the same infrastructure repeatedly across environments, teams create reusable modules that can be consumed by development, staging, and production environments.

At first, this approach appears to solve many operational problems.

Infrastructure becomes consistent.

Code duplication is reduced.

Changes can be implemented from a central location.

However, as platforms grow and environments begin to evolve independently, many organizations discover that excessive reusability introduces a different challenge: complexity.

The goal of DRY is to eliminate unnecessary duplication.

The goal is not to eliminate every difference that naturally exists within a platform.

Understanding this distinction is critical when designing Terraform at scale.

---

## Why DRY Exists

Consider an organization operating three environments:

```text
Development
Staging
Production
```

Without reusable modules, the repository may look like:

```text
dev-vpc.tf
stage-vpc.tf
prod-vpc.tf
```

Every environment contains similar networking definitions.

Every environment creates:

* VPCs
* Subnets
* Route Tables
* Internet Gateways
* Security Groups

Now imagine the networking team introduces a new security requirement.

Every environment must be updated individually.

If a bug is discovered, the fix must be applied everywhere.

As the number of environments grows, the operational burden increases.

Terraform modules solve this problem.

Instead of maintaining multiple copies of the same infrastructure, teams define a reusable module:

```text
modules/
└── vpc/
```

The module is then consumed by all environments.

This improves consistency, reduces duplication, and simplifies maintenance.

For many organizations, this is the first major step toward Infrastructure as Code maturity.

---

## The Point Where Reuse Begins to Break Down

The challenges begin when environments stop being identical.

Many Terraform examples assume that all environments should behave the same way.

Real-world platforms rarely operate that way.

Consider a Kubernetes platform deployed across three environments:

```text
Development
Staging
Production
```

Initially, all three environments may consume the same VPC module.

The architecture remains relatively simple.

As the platform evolves, production requirements begin to diverge.

Suppose production applications must be exposed externally using Gateway API.

The Kubernetes worker nodes run inside private subnets.

Outbound connectivity is required.

To support this architecture, NAT Gateways are introduced.

The production environment now looks like:

```text
Production

Internet
    │
NAT Gateway
    │
Private Subnets
    │
EKS Worker Nodes
```

However, development and staging environments may not require the same architecture.

To reduce cost:

```text
Development
├── No NAT Gateway

Staging
├── No NAT Gateway
```

The environments are no longer identical.

This is where many teams encounter the DRY dilemma.

---

## The Wrong Solution

A common reaction is to create environment-specific modules.

For example:

```text
vpc-dev
vpc-stage
vpc-prod
```

Initially this appears reasonable.

Each environment can evolve independently.

Each team can customize networking behavior.

However, the original duplication problem immediately returns.

Now every improvement must be repeated:

* Security fixes
* Bug fixes
* Compliance changes
* Networking enhancements

Over time, the modules begin to drift apart.

What started as a reusable architecture becomes a collection of independent implementations.

The maintenance burden increases significantly.

The same problem DRY originally solved begins to reappear.

---

## The Correct Use of DRY

DRY does not require every environment to behave identically.

DRY only requires common implementation logic to be defined once.

The shared VPC module may expose configuration options that allow environments to behave differently.

For example:

```text
VPC Module

Inputs

- create_nat_gateway
- private_subnet_count
- public_subnet_count
- enable_flow_logs
```

The environments can then consume the same module differently:

```text
Development
└── create_nat_gateway = false

Staging
└── create_nat_gateway = false

Production
└── create_nat_gateway = true
```

The implementation remains centralized.

The behavior remains flexible.

This is the intended purpose of Terraform modules.

---

## When Flexibility Becomes Complexity

The challenge is that flexibility often grows over time.

A module that originally contained three variables may eventually contain thirty.

Then fifty.

Then one hundred.

The VPC module may eventually expose:

```text
enable_nat_gateway
enable_flow_logs
enable_vpc_endpoints
enable_dns_support
enable_firewall
enable_transit_gateway
enable_inspection_vpc
enable_network_monitoring
```

Every new requirement introduces another variable.

Every new platform feature introduces another condition.

Eventually the module becomes extremely difficult to understand.

The infrastructure remains reusable.

The operational complexity increases dramatically.

This is one of the most common Terraform anti-patterns in large organizations.

The module becomes a framework instead of a reusable building block.

---

## The Cost of Cognitive Load

One of the most overlooked aspects of Terraform design is cognitive load.

Imagine a new engineer joining the platform team.

They need to understand how production networking works.

Instead of reviewing a simple VPC implementation, they must understand:

* Module inputs
* Module outputs
* Conditional resources
* Environment-specific variables
* Nested module calls

A simple networking change now requires understanding dozens of moving parts.

The infrastructure remains technically correct.

The operational complexity increases.

As platforms grow, engineers spend more time understanding infrastructure than creating it.

This is often the first sign that reusability has been pushed too far.

---

## Reusability Versus Maintainability

One of the most valuable lessons in platform engineering is that infrastructure code is read more frequently than it is written.

Engineers spend significant time:

* Reviewing changes
* Performing audits
* Investigating incidents
* Refactoring infrastructure
* Troubleshooting failures

As a result, maintainability becomes more important than maximizing reuse.

Many organizations eventually discover that:

```text
Less Duplication
≠
Less Complexity
```

A small amount of duplication may actually improve readability.

Sometimes twenty duplicated lines are easier to maintain than four additional abstraction layers.

The objective should not be maximum reuse.

The objective should be sustainable reuse.

---

## Platform Engineering and Golden Paths

Modern platform engineering teams often solve this problem using opinionated platform patterns.

Rather than exposing hundreds of variables, they create approved deployment patterns.

For example:

```text
Development Network Pattern

- No NAT Gateway
- Lower Cost
- Reduced Redundancy
```

```text
Production Network Pattern

- NAT Gateway
- Flow Logs
- High Availability
- Enhanced Security Controls
```

Both patterns may consume the same underlying VPC module.

The difference is that platform teams optimize for predictable outcomes rather than unlimited customization.

This approach reduces operational risk while still preserving reuse.

---

## Key Takeaways

* DRY solves duplication problems, not requirement differences.
* Environment-specific requirements naturally emerge as platforms grow.
* Creating separate modules for every environment often reintroduces duplication.
* Excessive flexibility can make modules difficult to understand and maintain.
* Infrastructure code should optimize for maintainability rather than maximum reuse.
* Platform engineering focuses on reusable building blocks combined with opinionated deployment patterns.
* The goal is not to eliminate every difference between environments. The goal is to manage those differences safely and consistently.

In the next file, we will explore how reusable modules begin depending on one another and how module coupling can transform a simple Terraform repository into a complex dependency graph.

