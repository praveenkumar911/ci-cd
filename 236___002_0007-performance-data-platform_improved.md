
# 0007. Performance Data Platform

## Status

- **Proposed**: 2021-06-16  
- **Partially Rejected**: 2022-09-26  
  - Partial rejection involved scaling back the infrastructure. SQS queues and lambdas were replaced with a simpler Docker-based approach for a feedback form running alongside the PHP app.

---

## Context

We have a requirement to publish performance data about the **Make an LPA service**. The key metrics we must track and publish are:

1. **User Satisfaction**: Ratings of the service provided by users on a 5-point scale ("Very Dissatisfied" to "Very Satisfied"). This data is currently collected through a [centralized government feedback page](https://www.gov.uk/done/lasting-power-of-attorney).
2. **Completion Rate**: Percentage of transactions started on the service that are subsequently completed. This is derived from Google Analytics and the database.
3. **Digital Take-Up**: Number of users using the online service as a percentage of users across all channels (online, paper, phone, etc.). Currently, there is no API for this metric.

Currently, this data is manually collated, which is inefficient and prone to errors. Automating this process will improve accuracy and reduce overhead.

As a secondary objective, this project provides an opportunity to test our proposed new technology stack (see [0006-modernise-the-code-base](./0006-modernise-the-code-base.md)).

---

## Decision Drivers

- **Efficiency**: Automate manual processes to reduce overhead and human error.
- **Scalability**: Ensure the solution can handle future growth in data volume and complexity.
- **Cost-Effectiveness**: Avoid over-engineering by balancing simplicity with functionality.
- **Alignment with Long-Term Goals**: Use this project as a stepping stone toward modernizing the codebase and adopting microservices architecture.

---

## Considered Options

1. **Embedded Architecture**:
   - Reuse existing infrastructure (e.g., PostgreSQL database, API proxy) to implement the data platform.
   - Minimize additional provisioning costs and complexity.
2. **Standalone Architecture**:
   - Implement the data platform as a completely isolated system with its own database, proxy, and ingress rules.
   - Provides greater separation but introduces significant complexity and cost.

---

## Decision Outcome

### Chosen Option: Embedded Architecture

The team decided on an **embedded architecture** for the following reasons:
- It reuses existing infrastructure, reducing complexity and cost.
- It aligns with the goal of incrementally modernizing the codebase without overhauling the entire system.
- A standalone architecture was deemed unnecessary for the relatively simple requirements of this service.

#### Positive Consequences
- Reduces the need for additional provisioning (e.g., separate databases, proxies).
- Aligns with long-term architectural goals by introducing modern components (e.g., Python Flask app, Docker).
- Automates manual processes, improving accuracy and efficiency.

#### Negative Consequences
- The embedded design may introduce coupling between the data platform and the existing application, potentially complicating future migrations.
- Limited scalability compared to a standalone architecture.

---

## Pros and Cons of the Options

### Embedded Architecture
- **Pros**:
  - Cost-effective and minimizes additional infrastructure.
  - Leverages existing components, reducing implementation time.
  - Provides a gradual transition toward modernization.
- **Cons**:
  - Potential for increased coupling between systems.
  - Limited scalability for future needs.

### Standalone Architecture
- **Pros**:
  - Complete separation of concerns, reducing coupling.
  - Easier to scale independently in the future.
- **Cons**:
  - Adds significant complexity and cost.
  - Requires additional provisioning (e.g., databases, proxies, ingress rules).

---

## Implementation Approach

1. **Data Sources**:
   - **User Satisfaction**: Collected via a new feedback form based on the existing centralized form.
   - **Completion Rate**: Calculated by counting LPA applications that reach a "created" state, excluding applications completed within a single session.
   - **Digital Take-Up**: Request a new endpoint on the Sirius data API to provide this data.

2. **High-Level Architecture**:
   - The data platform API is positioned behind the existing `api-web` proxy.
   - The worker process and API write to the existing PostgreSQL database but use new tables for storing performance data.
   - Diagrams illustrating the embedded architecture are available:
     - [System Landscape View](../images/structurizr-SystemLandscapeEmbedded.png)
     - [Container View](../images/structurizr-ContainerEmbedded.png)

3. **Implementation Details**:
   - The data platform is implemented in Python using AWS Lambdas (or Docker for local development).
   - A queue-based workflow using SQS is used to generate performance data and notify clients when ready.
   - User satisfaction data is stored in a separate table in the PostgreSQL database, managed using SQLAlchemy (with Alembic for migrations).
   - An interface in the admin UI allows admin users to view user satisfaction data.
   - Initially, user satisfaction data is collected via a PHP form in the front-end app, posting to the new Python API. This will serve as a test case for migrating to microfrontend slices.

---

## Consequences

### Pros
- Automates manual processes, reducing overhead and human error.
- Makes performance data easily accessible and requestable by other services (e.g., [data.gov.uk](https://data.gov.uk/)).
- Provides a foundation for modernizing the codebase incrementally.

### Cons
- The embedded design introduces some coupling between systems, which may complicate future migrations.
- Initial implementation effort is significant for a relatively simple use case.

---

## Glossary

- **LPA**: Lasting Power of Attorney.
- **SQS**: Amazon Simple Queue Service.
- **C4 Model**: A framework for visualizing software architecture (Context, Containers, Components, Code).
