---

# 0007 - Performance Data Platform

##  Metadata

- **Status**: Proposed (2021-06-16), Partially Rejected (2022-09-26)
- **Deciders**: [Names of Deciders]
- **Date**: 2021-09-06
- **Related ADRs**: [0006-modernise-the-code-base](./0006-modernise-the-code-base.md)

---

## Context and Problem Statement

The Make an LPA service currently relies on manual processes to collate and publish performance data. This includes:

- **User Satisfaction**: Ratings provided by users on a 5-point scale, collected via a centralized government feedback page.
- **Completion Rate**: The percentage of transactions started by users that are subsequently completed, derived from Google Analytics and the database.
- **Digital Take-Up**: The percentage of users using the online service compared to other channels (e.g., paper or phone), which is manually collected from Sirius (OPG's case management system).

Manual collation is time-consuming, error-prone, and inefficient. Automating this process will improve accuracy, reduce overhead, and make the data more accessible for stakeholders. Additionally, this project provides an opportunity to test the proposed modernized technology stack (see [0006-modernise-the-code-base](./0006-modernise-the-code-base.md)).

---

## Decision Drivers

- **Scalability**: Ensure the solution can handle future growth in data volume and complexity.
- **Ease of Implementation**: Minimize development effort while addressing key requirements.
- **Cost Efficiency**: Avoid unnecessary infrastructure or operational expenses.
- **Alignment with Long-Term Goals**: Use this project as a stepping stone toward adopting modern architectural practices and technologies.

---

## Considered Options

1. **Embedded Architecture**:
   - Leverage the existing application stack (e.g., PostgreSQL database, API proxy) to implement the data platform.
   - Pros:
     - Reuses existing infrastructure, reducing complexity and cost.
     - Aligns with long-term goals of modernizing the codebase incrementally.
   - Cons:
     - Tight coupling with the existing application may introduce risks if the platform scales significantly.

2. **Standalone Architecture**:
   - Implement the data platform as a completely separate system with its own database, proxy, and ingress rules.
   - Pros:
     - Fully decoupled from the existing application, offering greater flexibility.
   - Cons:
     - Adds significant complexity and cost (e.g., new database, Terraform scripting, load balancer).

---

## Decision Outcome

### Chosen Option: Embedded Architecture (Partially Implemented)

After discussions, we decided to adopt an **embedded architecture**, reusing parts of the existing stack to minimize complexity and cost. However, the original plan was scaled back due to concerns about over-engineering. Specifically:

- The **complex infrastructure** (e.g., SQS queues, AWS Lambda functions) was replaced with a simpler Docker-based approach.
- The solution now focuses on a **feedback form** running as a Python Flask application alongside the PHP app, which records user satisfaction scores.

This decision balances short-term implementation simplicity with long-term architectural goals.

---

## Consequences

### Positive Consequences

- **Automation**: Reduces manual effort and potential for human error in collating performance data.
- **Transparency**: Documents the assumptions and processes behind data gathering, making them visible to stakeholders.
- **Foundation for Modernization**: Provides a gateway to adopting modern technologies and practices incrementally.

### Negative Consequences

- **Limited Scope**: The scaled-back implementation does not fully automate all data sources (e.g., Digital Take-Up still requires manual intervention).
- **Technical Debt**: Tight coupling with the existing stack may create challenges if the platform needs to scale significantly in the future.

---

## Pros and Cons of the Options

### Embedded Architecture

- **Pros**:
  - Reuses existing infrastructure, reducing complexity and cost.
  - Aligns with long-term goals of modernizing the codebase incrementally.
- **Cons**:
  - Tight coupling with the existing application may limit scalability.

### Standalone Architecture

- **Pros**:
  - Fully decoupled from the existing application, offering greater flexibility.
- **Cons**:
  - Adds significant complexity and cost (e.g., new database, Terraform scripting, load balancer).

---

## Additional Notes

- **Acronyms Explained**:
  - **LPA**: Lasting Power of Attorney
  - **SQS**: Simple Queue Service (AWS)
  - **API**: Application Programming Interface
  - **C4 Model**: A framework for visualizing software architecture (https://c4model.com/)

- **Next Steps**:
  - Implement the Python Flask feedback form and integrate it with the existing PHP app.
  - Evaluate the feasibility of automating Digital Take-Up data collection in the future.

---
