\# Investigation 03 — Architecture Map



\## 1. Objective



The purpose of this investigation was to understand the structural relationship between routing, layouts, pages, shared components, hooks, the data/query layer, and the local mock service.



No source-code changes were made during this investigation.



\## 2. High-Level Architecture



```text

TanStack Router

&#x20;     │

&#x20;     ▼

Root Route (\_\_root.tsx)

&#x20;     │

&#x20;     ├── QueryClientProvider

&#x20;     │

&#x20;     ├── AppShell

&#x20;     │      │

&#x20;     │      ├── Navigation

&#x20;     │      └── Mock Service Controls

&#x20;     │

&#x20;     └── <Outlet />

&#x20;             │

&#x20;             ▼

&#x20;       Current Route Page

&#x20;             │

&#x20;             ▼

&#x20;       Shared Components

&#x20;             │

&#x20;             ▼

&#x20;         queries.ts

&#x20;             │

&#x20;             ▼

&#x20;        mock-api.ts

&#x20;             │

&#x20;             ▼

&#x20;      In-memory mock data

```



\## 3. Routing Layer



\### `src/router.tsx`



The application creates a TanStack Router instance using the generated route tree.



Responsibilities:



\- Creates a `QueryClient`.

\- Creates the TanStack Router.

\- Supplies the `QueryClient` through router context.

\- Enables scroll restoration.

\- Configures route preloading behavior.



The generated route structure is provided through:



```text

src/routeTree.gen.ts

```



\### `src/routes/\_\_root.tsx`



The root route provides the global application structure.



Responsibilities:



\- Defines application metadata.

\- Provides the global stylesheet.

\- Defines the 404 component.

\- Defines the root error component.

\- Wraps the application in `QueryClientProvider`.

\- Renders `AppShell`.

\- Renders nested routes through `<Outlet />`.

\- Provides the global toast component.



The `<Outlet />` is essential because it is the location where matched child routes are rendered.



\## 4. Page / Route Layer



Application pages are organized under:



```text

src/routes/

```



The route files correspond to the major SupportDesk Pro modules:



\- Workspace

\- Tickets

\- Customers

\- Knowledge Base

\- Team

\- SLA

\- Reports



Dynamic routes use TanStack Router's file-based naming convention, for example:



```text

tickets.$id.index.tsx

tickets.$id.edit.tsx

customers.$id.index.tsx

customers.$id.edit.tsx

knowledge-base.$id.index.tsx

knowledge-base.$id.edit.tsx

team.$id.tsx

```



\## 5. Shared Application Component Layer



Application-specific reusable components are located under:



```text

src/components/app/

```



The main application shell is:



```text

src/components/app/app-shell.tsx

```



`AppShell` provides:



\- Sidebar navigation

\- Application branding

\- Current-user header information

\- Responsive navigation behavior

\- Mock-service controls

\- Main content container



The navigation configuration directly defines the visible Workspace, Tickets, Customers, Knowledge, Team \& SLA, and Reports sections.



There is currently no Administration group in the `AppShell` navigation configuration.



This is consistent with the route-audit observation that the Administration URLs are not accessible, but the absence of the navigation group alone does not determine whether the routes were intentionally omitted or simply not implemented.



\## 6. Shared UI Layer



Reusable UI primitives are located under:



```text

src/components/ui/

```



Examples include:



\- Button

\- Card

\- Dialog

\- Input

\- Select

\- Switch

\- Table

\- Tabs

\- Toast/Sonner

\- Pagination

\- Skeleton

\- Tooltip



These components provide the common visual building blocks used by application pages.



\## 7. Data / Query Layer



\### `src/lib/queries.ts`



This file acts as the TanStack Query abstraction layer.



It provides query hooks such as:



```text

useTickets

useTicket

useCustomers

useCustomer

useArticles

useArticle

useAgents

useAgent

useSlaPolicies

useBreaches

useActivity

useStats

```



It also defines centralized query keys.



The query functions call the `api` object from:



```text

src/lib/mock-api.ts

```



Mutations are handled through the shared `useApiMutation()` helper.



The mutation helper:



1\. Executes the supplied mutation function.

2\. Invalidates TanStack Query data after success.

3\. Displays a success toast when configured.

4\. Displays an error toast when the mutation fails.



\## 8. Mock Service Layer



\### `src/lib/mock-api.ts`



The mock API is a local asynchronous service.



It provides:



\- Seeded agents

\- Seeded customers

\- Seeded tickets

\- Seeded knowledge articles

\- SLA policies

\- Activity records

\- Ticket/customer/article/SLA operations

\- Statistics

\- Simulated latency

\- Simulated failures

\- Forced outage simulation



The service uses a `call()` wrapper that introduces configurable latency and can simulate service failures.



The current service settings include:



```text

latency

failureRate

forceError

```



These settings are exposed through the Mock Service controls in `AppShell`.



\## 9. Data Persistence Observation



The mock service stores its seeded and modified records in module-level in-memory arrays.



Examples include:



```text

agents

customers

tickets

articles

slaPolicies

activities

```



Create/update operations modify these in-memory structures.



Therefore, changes made during a browser session are available to the running application but are not persisted to a database or browser storage.



This explains the earlier route-testing observation where newly created customer and ticket records disappeared after a full browser refresh.



This is documented as an architectural characteristic of the current mock service and is not classified as a cleanup defect.



\## 10. Query and Service Relationship



The intended data flow is:



```text

Route Component

&#x20;     │

&#x20;     ▼

TanStack Query Hook

&#x20;     │

&#x20;     ▼

queries.ts

&#x20;     │

&#x20;     ▼

mock-api.ts

&#x20;     │

&#x20;     ▼

call()

&#x20;     │

&#x20;     ├── Simulated latency

&#x20;     ├── Simulated errors

&#x20;     │

&#x20;     ▼

In-memory data

```



For mutations:



```text

Route Component

&#x20;     │

&#x20;     ▼

useApiMutation()

&#x20;     │

&#x20;     ▼

mock-api mutation

&#x20;     │

&#x20;     ▼

Update in-memory data

&#x20;     │

&#x20;     ▼

invalidateQueries()

&#x20;     │

&#x20;     ▼

Affected pages refresh their data

```



\## 11. Hooks Layer



Custom React hooks are located under:



```text

src/hooks/

```



The current application-specific hook structure is small.



The primary custom hook identified in this directory is:



```text

use-mobile.tsx

```



TanStack Query hooks are centralized separately in:



```text

src/lib/queries.ts

```



\## 12. Testing Layer



Tests are located under:



```text

src/test/

```



Current files include:



```text

app-routing.test.tsx

setup.ts

```



The existing test structure includes application routing coverage.



\## 13. Architecture Summary



The application follows a relatively clear layered structure:



```text

Routing

&#x20;  ↓

Root/Layout

&#x20;  ↓

Pages

&#x20;  ↓

Shared Components

&#x20;  ↓

Query Hooks

&#x20;  ↓

Mock API

&#x20;  ↓

In-Memory Data

```



The separation between the query layer and mock service makes the data-access logic easier to identify and provides a clear boundary for later investigation.



\## 14. Investigation Findings



\### Finding 1 — Clear application shell



`AppShell` acts as the shared layout and navigation layer for the application.



\*\*Status:\*\* Working as intended.



\### Finding 2 — Centralized query layer



TanStack Query hooks are centralized in `src/lib/queries.ts`.



\*\*Status:\*\* Clear architectural separation observed.



\### Finding 3 — Local mock service



Application data access is centralized through `src/lib/mock-api.ts`.



\*\*Status:\*\* Clear architectural separation observed.



\### Finding 4 — In-memory persistence



The mock service stores records in memory rather than persistent storage.



\*\*Status:\*\* Architectural characteristic; not classified as a defect.



\### Finding 5 — Administration navigation absent



The navigation configuration does not contain an Administration group.



This corresponds with the route audit finding that the four required Administration URLs currently return 404.



\*\*Status:\*\* Requires further investigation.



\## 15. Scope Boundary



This investigation documents the existing architecture only.



No components were removed, no routes were added, and no data-layer code was modified.



Further dependency, component, hook, service, and performance investigations will be performed separately.

