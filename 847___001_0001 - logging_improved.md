
# 0001. Logging

> **Status**: Accepted  
> **Date**: 2020-Mar-09  

---

## Context

When designing a logging mechanism for the system, two primary approaches were considered:

1. **Machine-Readable Logs**: Tools like [Bunyan](https://github.com/trentm/node-bunyan) prioritize structured, machine-readable logs (e.g., JSON format).  
2. **Human-Readable Logs**: Tools like [Winston](https://github.com/winstonjs/winston) focus on producing logs that are easy for humans to read directly.

The challenge lies in balancing these two priorities. Human-readable logs are immediately accessible but harder to parse programmatically. Conversely, machine-readable logs are easier to process and analyze but less intuitive for direct human consumption.

Given the long-term needs of the system, the decision was made to prioritize **machine-readable logs** as the primary format. This choice ensures logs can be easily consumed by tools and platforms while allowing for translation into human-readable formats when necessary.

---

## Decision Drivers

- **Scalability**: Logs need to be transported and processed by multiple systems (e.g., log aggregation platforms like ELK).  
- **Future-Proofing**: Machine-readable logs are more stable over time, whereas human-readable formats may drift or change unpredictably.  
- **Tooling Support**: Translating machine-readable logs into human-readable formats is straightforward, whereas the reverse is often error-prone and complex.  
- **Operational Needs**: Logs must support both operational debugging and automated analysis.

---

## Decision Outcome

### Chosen Option: Machine-Readable Format

It was decided that all logging will use a **machine-readable format** (e.g., JSON) as the first-class citizen. If needed, a utility or tool will be provided to translate these logs into a human-readable format.

#### Positive Consequences
- Logs are structured and consistent, making them ideal for automated processing and integration with tools like ELK stacks.  
- Easier to implement log parsing, aggregation, and analysis workflows.  
- Future-proof design minimizes risks associated with evolving log formats.

#### Negative Consequences
- Logs written directly to disk may be difficult for users to interpret without additional tooling.  
- Requires development of a translation tool if human-readable logs are frequently needed.

---

## Implementation Details

1. **Logging Framework**: Use a machine-readable logging library such as Bunyan.  
2. **Log Format**: Logs will be output in JSON format, with key-value pairs for all relevant information (e.g., timestamp, log level, message, metadata).  
3. **Translation Tool**: If necessary, develop a utility to convert machine-readable logs into a human-friendly format (e.g., plain text or formatted tables).  
4. **Transport Mechanisms**: Logs will be transportable to various destinations, including:
   - Rolling files for local storage.
   - Log aggregation platforms like ELK for centralized analysis.

---

## Examples

### Machine-Readable Log Example
```json
{
  "timestamp": "2020-03-09T12:34:56Z",
  "level": "info",
  "message": "User logged in successfully",
  "metadata": {
    "userId": "12345",
    "ipAddress": "192.168.1.1"
  }
}
```

### Human-Readable Translation (Hypothetical)
```
[2020-03-09 12:34:56] INFO: User logged in successfully
  UserID: 12345
  IP Address: 192.168.1.1
```

---

## Consequences

### Pros
- Logs are highly structured and consistent, enabling seamless integration with log aggregation and analysis tools.  
- Future changes to logging requirements can be accommodated without significant rework.  
- Reduces the risk of format drift over time, ensuring logs remain machine-parsable.

### Cons
- Directly reading logs from disk may be challenging for users unfamiliar with JSON or structured formats.  
- Additional effort may be required to develop and maintain a translation tool for human-readable logs.

---

## Glossary

- **Bunyan**: A Node.js library for creating structured, machine-readable logs in JSON format.  
- **Winston**: A popular Node.js logging library that emphasizes human-readable logs.  
- **ELK Stack**: A log aggregation and analysis platform consisting of Elasticsearch, Logstash, and Kibana.
