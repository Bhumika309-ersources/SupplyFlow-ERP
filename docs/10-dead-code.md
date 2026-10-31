\# Dead Code Audit



\## Objective



Identify code that is defined but never used, including:



\- Unused functions

\- Unused components

\- Unused exports

\- Unused constants

\- Unreachable logic

\- Dead imports

\- Obsolete helper code



No code was deleted or modified during this investigation.



\## Investigation Method



The audit used targeted repository searches with `findstr` to trace potentially unused exported functions, utilities, constants, and helpers back to their usage sites.



The investigation focused particularly on utility and infrastructure files because application components and route files had already been reviewed in the component and architecture audits.



\## Findings



\### 1. CSV Export Utility



\*\*File:\*\* `src/lib/csv.ts`



`downloadCsv` is exported from the CSV utility and is actively used by both report pages:



\- `src/routes/reports.agents.tsx`

\- `src/routes/reports.tickets.tsx`



\*\*Classification:\*\* KEEP



The utility provides shared CSV export functionality and is not dead code.



\### 2. Error Capture Utilities



\*\*File:\*\* `src/lib/error-capture.ts`



The following functions were investigated:



\- `describeError`

\- `consumeLastCapturedError`



`describeError` is used internally by the error capture implementation.



`consumeLastCapturedError` is imported and used by `src/server.ts` when handling SSR errors.



\*\*Classification:\*\* KEEP



These functions are part of the server-side error handling flow.



\### 3. Error Page Renderer



\*\*File:\*\* `src/lib/error-page.ts`



`renderErrorPage` is used by:



\- `src/server.ts`

\- `src/start.ts`



It therefore participates in server/startup error responses.



\*\*Classification:\*\* KEEP



\### 4. Page Head Helper



\*\*File:\*\* `src/lib/head.ts`



`pageHead` is used throughout the route definitions to provide page metadata.



Examples include:



\- Activity Log

\- Customers

\- Dashboard

\- Knowledge Base

\- Reports

\- SLA pages

\- Team pages

\- Ticket pages



\*\*Classification:\*\* KEEP



This is an actively reused route-level helper.



\### 5. Lovable Error Reporting



\*\*File:\*\* `src/lib/lovable-error-reporting.ts`



`reportLovableError` is imported and used by the root route error boundary.



\*\*Classification:\*\* KEEP



It is part of the application's error reporting path.



\### 6. Current Agent Constant



\*\*File:\*\* `src/lib/mock-api.ts`



`CURRENT\_AGENT\_ID` is used by:



\- Mock API activity/comment/article creation

\- `src/routes/tickets.assigned.tsx`



\*\*Classification:\*\* KEEP



The constant is required to represent the currently logged-in mock agent.



\### 7. Breach Detection Helper



\*\*File:\*\* `src/lib/mock-api.ts`



`isBreached` is used in several places:



\- `src/components/app/ticket-table.tsx`

\- `src/routes/tickets.$id.index.tsx`

\- Mock API breach listing

\- Agent statistics

\- Overall statistics



\*\*Classification:\*\* KEEP



This is an actively reused business-rule helper.



\## Summary



| Area | Finding | Classification |

|---|---|---|

| CSV utility | Used by two report routes | KEEP |

| Error capture | Used internally and by server | KEEP |

| Error page | Used by server and startup code | KEEP |

| Page metadata | Used across route definitions | KEEP |

| Error reporting | Used by root error boundary | KEEP |

| Current agent | Used by API and assigned-ticket route | KEEP |

| SLA breach detection | Used by multiple application areas | KEEP |



\## Dead Code Result



\*\*No confirmed dead code was identified during this targeted audit.\*\*



The investigated exports and helpers all have active usage paths. Therefore, no deletion or refactoring is recommended from this investigation.



\## Cleanup Recommendation



No cleanup action should be added to the Day 3 cleanup branch based on the findings from this audit.



Any future dead-code investigation should use the same approach: verify usage across application code, route definitions, server code, and configuration before classifying an item as dead.



\## Investigation Status



\*\*Completed — no dead-code cleanup required.\*\*

