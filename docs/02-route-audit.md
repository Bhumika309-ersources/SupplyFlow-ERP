\# Investigation 02 — Route Audit



\## 1. Objective



The purpose of this investigation was to verify the application's route coverage and identify routes that load correctly, routes that are missing, and unexpected routing behavior.



No source-code changes were made during this investigation.



\## 2. Environment



\- Project: SupportDesk Pro

\- Framework: React + TypeScript

\- Development server: Vite

\- Local URL: `http://localhost:8080`



\## 3. Route Verification



\### Workspace



| Route | Result |

|---|---|

| `/` | Loads correctly |

| `/dashboard` | Loads correctly |

| `/dashboard/analytics` | Loads correctly |

| `/activity-log` | Loads correctly |



\### Tickets



| Route | Result |

|---|---|

| `/tickets` | Loads correctly |

| `/tickets/new` | Loads correctly |

| `/tickets/:id` | Loads correctly using an existing ticket |

| `/tickets/:id/edit` | Loads correctly |

| `/tickets/assigned` | Loads correctly |

| `/tickets/escalations` | Loads correctly |



\### Customers



| Route | Result |

|---|---|

| `/customers` | Loads correctly |

| `/customers/new` | Loads correctly |

| `/customers/:id` | Loads correctly |

| `/customers/:id/edit` | Loads correctly |



\### Knowledge Base



| Route | Result |

|---|---|

| `/knowledge-base` | Loads correctly |

| `/knowledge-base/new` | Loads correctly |

| `/knowledge-base/:id` | Loads correctly |

| `/knowledge-base/:id/edit` | Loads correctly |



\### Team \& SLA



| Route | Result |

|---|---|

| `/team` | Loads correctly |

| `/team/:id` | Loads correctly |

| `/sla/policies` | Loads correctly |

| `/sla/breaches` | Loads correctly |



SLA policy editing was also tested. Saving an edited policy worked correctly.



The mock-service latency control was also tested. The Active toggle responds correctly when the mock-service latency is set to a reasonable value. The observed delay with higher latency is consistent with the simulated latency and is not considered a defect at this stage.



\### Reports



| Route | Result |

|---|---|

| `/reports` | Loads correctly |

| `/reports/tickets` | Loads correctly |

| `/reports/agents` | Loads correctly |



\### Administration



The following routes were tested directly by entering their URLs because an Administration section was not visible in the application's sidebar:



| Route | Result |

|---|---|

| `/settings/profile` | 404 Page Not Found |

| `/settings/users` | 404 Page Not Found |

| `/settings/notifications` | 404 Page Not Found |

| `/notifications` | 404 Page Not Found |



These routes are specified as required Administration routes in the assignment, so their current 404 behavior should be investigated further.



\### Catch-all Route



An intentionally invalid route was tested:



`/this-route-does-not-exist`



Result:



\*\*404 Page Not Found\*\*



This behavior is expected for the application's catch-all route.



\## 4. Additional Observations



\### Session-only mock data



A new customer and a new ticket were created during route testing. Both appeared correctly during the current browser session.



After refreshing the browser, the newly created records disappeared while the seeded records remained.



This observation is not classified as a defect during the route audit. It will be investigated later as part of the mock API/service investigation to determine whether the application's mock data is intentionally stored only in memory.



\## 5. Findings



\### Finding 1 — Administration routes unavailable



Four required Administration routes currently return the application's 404 page:



\- `/settings/profile`

\- `/settings/users`

\- `/settings/notifications`

\- `/notifications`



\*\*Severity:\*\* Investigation finding  

\*\*Current classification:\*\* Requires further investigation  

\*\*Action:\*\* Inspect route definitions and generated route tree before making any changes.



\### Finding 2 — Catch-all 404 works



An invalid route correctly displays the 404 page.



\*\*Status:\*\* Working as expected.



\### Finding 3 — Other required route groups



Workspace, Tickets, Customers, Knowledge Base, Team \& SLA, and Reports routes tested successfully.



\*\*Status:\*\* No routing issue observed during manual testing.



\## 6. Scope Boundary



No routing code was modified during this investigation.



The missing Administration routes will not be implemented or removed as part of this investigation. Their cause will be determined during later source-code and architecture investigation.



\## 7. Conclusion



The majority of the tested SupportDesk Pro routes are accessible and functional.



The main routing finding is that four Administration routes specified by the assignment are currently returning 404 responses. The application's catch-all 404 behavior itself is working correctly.



Further investigation of the route definitions and generated route tree is required before determining whether the Administration routes are missing, intentionally excluded, or otherwise misconfigured.

