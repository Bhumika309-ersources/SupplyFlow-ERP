\# Investigation 08 — Dependency Audit



\## Objective



Review the project's declared dependencies and determine whether they are actively required by the application, build system, testing setup, linting configuration, or supporting tooling.



The audit does \*\*not\*\* remove or modify dependencies. A dependency is only considered a removal candidate when there is sufficient evidence that it is unused and not required indirectly by project tooling.



\---



\## Audit Method



Dependencies were evaluated using four sources of evidence:



1\. Direct imports in application source code.

2\. Usage in configuration files such as Vite, Vitest, ESLint, and TypeScript configuration.

3\. Package scripts and development tooling requirements.

4\. Lockfile and package relationships, including peer-dependency relationships.



Absence from `src/` alone was not treated as evidence that a dependency is unused because build and development dependencies may only appear in configuration or scripts.



\---



\## Application Dependencies



\### Clearly used dependencies



The following dependencies have direct evidence of usage in the application:



| Dependency | Evidence | Decision |

|---|---|---|

| `@tanstack/react-query` | Query hooks and QueryClient setup | KEEP |

| `@tanstack/react-router` | Route definitions and navigation | KEEP |

| `@tanstack/react-start` | Server/startup and generated route integration | KEEP |

| `lucide-react` | Icons across shared components and route pages | KEEP |

| `recharts` | Dashboard and report charts | KEEP |

| `react-hook-form` | Shared form component | KEEP |

| `react-day-picker` | Calendar component | KEEP |

| `embla-carousel-react` | Carousel component | KEEP |

| `react-resizable-panels` | Resizable component | KEEP |

| `cmdk` | Command component | KEEP |

| `input-otp` | OTP UI component | KEEP |

| `vaul` | Drawer component | KEEP |

| `sonner` | Application toast notifications | KEEP |

| `clsx` | Class utility in `src/lib/utils.ts` | KEEP |

| `tailwind-merge` | Class merging in `src/lib/utils.ts` | KEEP |

| `class-variance-authority` | Shared UI component variants | KEEP |

| `zod` | Route/filter validation | KEEP |



The Radix UI packages with corresponding wrappers under `src/components/ui/` were also found to be actively represented in the application component layer and are therefore classified as KEEP.



\---



\## Build and Development Dependencies



Some dependencies are intentionally absent from application source files because they support development, testing, or building.



\### Vite / TanStack build tooling



`@lovable.dev/vite-tanstack-config` is imported by `vite.config.ts` and provides the project's integrated Vite/TanStack configuration.



Its documented configuration comments indicate that it supplies or integrates TanStack Start, React/Vite, Tailwind, TypeScript path handling, Nitro, environment handling, deduplication, and related build functionality.



The package also declares important peer dependencies including `@tailwindcss/vite`, `@tanstack/react-start`, `@vitejs/plugin-react`, `nitro`, `vite`, and `vite-tsconfig-paths`. Therefore, these packages cannot be classified as unused simply because they do not appear as direct imports in application code.



\*\*Decision: KEEP build tooling.\*\*



\### Testing dependencies



`vitest.config.ts` directly uses:



\- `vitest`

\- `@vitejs/plugin-react`



The project also defines the `test` and `test:watch` scripts using Vitest.



Testing dependencies are therefore required even though they are not imported by normal application routes.



\*\*Decision: KEEP.\*\*



\### Linting dependencies



`eslint.config.js` directly uses:



\- `@eslint/js`

\- `eslint-plugin-prettier`

\- `globals`

\- `eslint-plugin-react-hooks`

\- `eslint-plugin-react-refresh`

\- `typescript-eslint`



The project also exposes an `npm run lint` script.



\*\*Decision: KEEP.\*\*



\### TypeScript dependencies



`tsconfig.json` includes the application source as well as:



\- `vite.config.ts`

\- `vitest.config.ts`

\- `eslint.config.js`



The project therefore requires TypeScript and its associated type packages during development and tooling checks.



\*\*Decision: KEEP.\*\*



\---



\## Dependencies Requiring Indirect Build-Usage Consideration



The following packages should not be removed based solely on source-import searches:



| Dependency | Evidence | Decision |

|---|---|---|

| `@tailwindcss/vite` | Peer dependency of the Lovable TanStack Vite configuration; Tailwind integration | INVESTIGATE |

| `vite-tsconfig-paths` | Peer dependency of the Lovable configuration; project uses `@/\*` path aliases | INVESTIGATE |

| `@tanstack/router-plugin` | Present in TanStack build/route tooling and lockfile relationships | INVESTIGATE |

| `tailwindcss` | Project uses Tailwind styling and the Tailwind Vite integration | INVESTIGATE |



These are intentionally left as INVESTIGATE rather than REMOVE because their role may be indirect.



\---



\## Lockfile Evidence



The project's `bun.lock` contains the declared build-related packages and their relationships.



The Lovable Vite/TanStack configuration package declares peer dependencies including `@tailwindcss/vite`, `@tanstack/react-start`, `@vitejs/plugin-react`, `nitro`, `vite`, and `vite-tsconfig-paths`.



The lockfile also contains resolved entries for `@tanstack/react-start`, `@tanstack/router-plugin`, `tailwindcss`, and `vite-tsconfig-paths`.



This provides additional evidence that these packages participate in the project's build/tooling ecosystem rather than being arbitrary unused declarations.



\---



\## Bun Configuration



The repository contains `bunfig.toml`.



It configures Bun installation behavior, including a minimum package release age of 24 hours and explicit exclusions for selected Lovable packages.



This confirms that Bun-related project metadata exists and should be considered when reviewing package-management behavior.



No package-manager migration or lockfile replacement was performed as part of this audit.



\---



\## Security / Audit Observation



Previous dependency auditing reported npm vulnerabilities. `npm audit fix` was attempted, but not all findings could be resolved without potentially introducing breaking or out-of-range dependency changes.



No `npm audit fix --force` operation was performed.



Dependency upgrades or vulnerability remediation are outside the scope of this investigation because changing dependency versions during an unused-dependency audit could alter application behavior and invalidate the baseline.



These findings should be handled separately through a reviewed dependency/security update.



\---



\## Cleanup Decision



\### KEEP



Dependencies with clear application, build, test, lint, or configuration usage should remain unchanged.



\### INVESTIGATE



Packages whose usage is indirect or supplied through build tooling should remain until their role is confirmed through controlled build/configuration testing.



\### REMOVE



\*\*No dependency is recommended for immediate removal from this investigation.\*\*



There is insufficient evidence to safely remove any declared dependency without further controlled testing.



\---



\## Risk Assessment



| Finding | Risk | Proposed Action |

|---|---|---|

| Large number of UI/Radix dependencies | Medium | Keep while corresponding UI wrappers remain present |

| Build dependencies not imported by `src/` | Low | Keep because configuration/tooling uses them |

| Indirect Vite/TanStack dependencies | Medium | Investigate only if cleanup is later approved |

| npm audit vulnerabilities | Medium | Handle as a separate dependency-security task |

| Bun lock/configuration present | Low | Preserve current package-management metadata |



\---



\## Verification



The following evidence was reviewed:



\- `package.json`

\- `vite.config.ts`

\- `vitest.config.ts`

\- `eslint.config.js`

\- `components.json`

\- `tsconfig.json`

\- `bunfig.toml`

\- `bun.lock`

\- application source imports



No dependency modifications were made during the audit.



\---



\## Conclusion



The dependency audit found extensive legitimate usage across application code and development tooling.



The key conclusion is that \*\*a dependency not imported from `src/` is not automatically unused\*\*. Build plugins, test runners, linting tools, framework integrations, and peer dependencies may be required outside the application source tree.



No dependency has sufficient evidence to be safely removed at this stage.



Further cleanup, if approved, should be performed as a controlled change with a build, lint, test, and affected-route verification after each dependency change.

