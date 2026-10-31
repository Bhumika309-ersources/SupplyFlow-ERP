\# Investigation 06 — Services \& Mock API Audit



\## 1. Objective



Review the local mock service implementation under `src/lib/mock-api.ts` to determine:



\- How service data is structured

\- How simulated API behavior is implemented

\- Whether CRUD operations cover the application's required workflows

\- How loading and error states are simulated

\- How mutations affect related data

\- Whether persistence behavior is appropriate for the project

\- Whether any service-level cleanup or risk candidates exist



No code changes were made during this investigation.



\## 2. Service Structure



The application uses a single local asynchronous mock service:



```text

src/lib/mock-api.ts

```



The service contains:



\- Type definitions

\- Service controls

\- Asynchronous request simulation

\- Seed-data generation

\- Activity logging

\- Ticket/SLA helper functions

\- API operations



This provides a centralized data/service layer for the application.



\## 3. Data Models



The mock service defines the main domain models used by the application:



\- `Agent`

\- `Customer`

\- `Comment`

\- `Ticket`

\- `Article`

\- `SlaPolicy`

\- `Activity`



Ticket-related types include status, priority, channel, customer, assignee, tags, SLA information, escalation information, and comments.



The data model therefore supports the major SupportDesk Pro workflows.



\## 4. Service Simulation



The service provides configurable simulation controls through:



```text

ServiceSettings

```



The settings include:



\- `latency`

\- `failureRate`

\- `forceError`



The default latency is 450 ms, while the default failure rate is zero and forced errors are disabled.



The service request wrapper applies randomized latency before executing the requested operation.



It can also deliberately throw a simulated 503-style `ServiceError`.



This supports testing of:



\- Loading states

\- Slow requests

\- Random service failures

\- Forced service outages

\- Retry/error handling



The implementation therefore aligns with the assignment requirement for realistic asynchronous mock behavior.



\## 5. Seed Data



The service generates deterministic mock data using a seeded random-number generator.



The seeded dataset contains:



\- 10 agents

\- 36 customers

\- 84 tickets

\- 14 knowledge-base articles

\- 4 SLA policies

\- 60 activity records



The generated data includes realistic combinations of ticket status, priority, channel, assignments, escalation state, comments, customer tiers, and article status.



This provides sufficient data for testing tables, filters, dashboards, reports, SLA views, and related workflows.



\## 6. Ticket Service Operations



The ticket API provides operations for:



\- Listing tickets

\- Searching/filtering tickets

\- Retrieving an individual ticket

\- Creating tickets

\- Updating tickets

\- Adding comments

\- Deleting tickets



Ticket filters support:



\- Search query

\- Status

\- Priority

\- Assignee

\- Escalation state

\- Customer



Mutating ticket operations update the in-memory dataset and record corresponding activity events.



This supports the primary ticket-management workflows.



\## 7. Customer Service Operations



The customer API provides:



\- Customer listing/search

\- Customer retrieval

\- Customer creation

\- Customer updates



Customer search checks the customer's name, company, and email.



Customer mutations also generate activity-log entries.



No unused customer service operation was identified.



\## 8. Knowledge Base Operations



The article API provides:



\- Article listing/search

\- Article retrieval

\- Article creation

\- Article updates

\- Helpful/not-helpful voting



Article creation initializes view and feedback counters and associates the article with the current agent.



Article updates refresh the article's `updatedAt` value.



This supports the knowledge-base workflows required by the application.



\## 9. Agent, SLA, and Activity Services



The service provides:



\### Agents



\- List agents

\- Retrieve an individual agent



\### SLA



\- List SLA policies

\- Update SLA policies

\- Calculate/list current SLA breaches



\### Activity



\- List recent activity records

\- Add activity entries through mutation operations



These operations support team management, SLA management, activity history, and reporting screens.



\## 10. Statistics and Reporting



The `stats()` service operation calculates dashboard/reporting information from the current ticket and agent data.



It provides:



\- Total tickets

\- Open tickets

\- Unassigned tickets

\- Escalated tickets

\- Breached tickets

\- Solved tickets

\- CSAT

\- Average first-response time

\- Ticket counts by day

\- Ticket counts by status

\- Ticket counts by priority

\- Ticket counts by channel

\- Agent-level statistics



This allows dashboard and reporting screens to consume derived service data rather than maintaining separate copies of the same calculations.



\## 11. Persistence Assessment



The mock data is stored in module-level in-memory arrays.



Therefore:



\- Mutations persist while the application session remains active.

\- Browser refresh resets the data to the seeded state.

\- There is no external database or browser persistence layer.



This behavior explains why records created during a session can disappear after a browser refresh.



\### Decision



\*\*KEEP\*\*



\### Reason



The project specification calls for a local asynchronous mock service rather than a production database. Session-only in-memory persistence is therefore appropriate for this implementation.



The refresh reset should be documented as a known characteristic rather than treated as a defect.



\## 12. Data Isolation



The asynchronous service wrapper returns:



```text

structuredClone(fn())

```



This means service results are cloned before being returned to consumers.



This provides a useful boundary between the internal mock-service state and data consumed by the UI.



No cleanup action is recommended.



\## 13. Activity Logging



Mutation operations generally record activity events.



Examples include:



\- Ticket creation

\- Ticket updates

\- Ticket comments

\- Ticket deletion

\- Customer creation/update

\- Article creation/update

\- SLA policy updates



This keeps the Activity Log connected to important mutations.



No duplicate activity logging mechanism was identified during this audit.



\## 14. Potential Risk Candidate — Ticket ID Generation



A potential robustness issue was identified in ticket creation.



New ticket IDs are generated using the current ticket-array length:



```text

T-${1000 + tickets.length}

```



Because the value depends on the current array length, deleting a ticket and subsequently creating another ticket could potentially produce an ID that has already been used.



\### Decision



\*\*INVESTIGATE\*\*



\### Risk



Potential duplicate ticket IDs after a delete/create sequence.



\### Proposed verification



During the controlled cleanup/verification phase:



1\. Record the current ticket IDs.

2\. Delete a ticket.

3\. Create a new ticket.

4\. Compare the new ticket ID against existing IDs.

5\. Verify that ticket detail navigation still resolves correctly.



No code change should be made until this behavior is confirmed.



\## 15. Other Observations



The service contains deterministic reporting values such as CSAT and average response time in the statistics response.



These appear to be mock values intended to provide realistic dashboard/reporting output.



They should not be treated as production analytics.



No cleanup action is recommended because they serve the purpose of the mock implementation.



\## 16. Cleanup Register Summary



| Finding | Evidence | Decision | Action |

|---|---|---|---|

| Local in-memory persistence | Data stored in module-level arrays | KEEP | No change |

| Simulated latency/failure controls | Central `ServiceSettings` and request wrapper | KEEP | No change |

| Seeded realistic dataset | Deterministic seeded records across entities | KEEP | No change |

| Activity logging | Mutation operations call activity logger | KEEP | No change |

| Structured cloning of responses | Service wrapper returns cloned data | KEEP | No change |

| Ticket ID based on array length | Ticket creation uses `1000 + tickets.length` | INVESTIGATE | Verify delete/create sequence |



\## 17. Conclusion



The mock API is a central and important part of the application's architecture.



It provides realistic asynchronous behavior, seeded data, controllable failures, CRUD operations, activity logging, SLA calculations, and reporting statistics.



No broad refactor or deletion is recommended.



The only cleanup/risk candidate identified is the ticket ID generation strategy, which should be verified before any change is considered.



\*\*Status: AUDITED — one targeted investigation candidate identified; no code changes recommended yet.\*\*

