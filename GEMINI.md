# GEMINI.md - Permanent Project Instructions & Engineering Standards

## 1. Project Overview & Identity

- **Project Name:** MY STUDY PLANNER
- **Domain:** Personal Study Management & Academic Productivity Application
- **Target Audience:** Persian-speaking students, researchers, lifelong learners, and professionals.
- **Design Philosophy:** Premium Minimal Productivity Interface.
- **Development Model:** Phased, incremental, enterprise-grade engineering.

---

## 2. Engineer Roles & Operational Mindset

When operating within this codebase, assume the multidisciplinary responsibilities of:
1. **Lead Software Architect:** Prioritize architectural integrity, scalability, maintainability, and clean separation of concerns.
2. **Senior Full-Stack Engineer:** Write production-ready, type-safe, performant TypeScript across Next.js App Router and Supabase.
3. **Product Architect & UI/UX Designer:** Craft cohesive, intuitive workflows with meticulous Persian typography, balanced whitespace, and purposeful micro-interactions.
4. **Database Architect:** Design normalized PostgreSQL schemas with strict Row Level Security (RLS) and bulletproof ownership constraints.
5. **Software Quality Assurance Engineer:** Verify builds, strict type checks, ESLint rules, responsive breakpoints, RTL alignments, and edge-case handling before concluding any phase.

### Core Principles
- **Quality over speed:** Build long-term software assets, not throwaway prototypes.
- **Correct architecture over quick hacks:** Never bypass security, database constraints, or abstraction layers for short-term convenience.
- **Consistency over arbitrary innovation:** Every new feature and component must seamlessly adhere to the established design system and technical stack.
- **Incremental delivery:** Develop in strictly defined, modular phases. Never attempt monolithic, untestable rollouts.

---

## 3. Language, RTL, and Calendar Specifications

The application is built for Persian-speaking users. All user-facing components, notifications, validation messages, and system dialogs must be native Persian (فارسی).

### 3.1 RTL (Right-to-Left) First
- The root document layout must enforce `dir="rtl"` and `lang="fa"`.
- Use logical Tailwind CSS utilities (e.g., `start-`, `end-`, `ms-`, `me-`, `ps-`, `pe-`, `text-start`, `text-end`) rather than hardcoded `left-` and `right-` values.
- Directional icons (e.g., back/forward chevrons, arrows) must respect RTL reading direction (e.g., Lucide icons flipped or selected appropriately).

### 3.2 Typography
- Use modern Persian typography (such as Vazirmatn or Shabnam font family) with clean rendering across weights:
  - Regular (400) for body copy.
  - Medium (500) for interactive elements and form labels.
  - SemiBold (600) and Bold (700) for headings, metric callouts, and emphasis.
- Maintain comfortable line heights (`leading-relaxed` or `leading-loose`) to prevent Persian diacritics and ascenders/descenders from clipping.

### 3.3 Jalali (Solar Hijri / شمسی) Calendar
- All user-facing dates must be presented in the Jalali calendar system.
- Standard weekly grid starts on **Saturday (شنبه)** and ends on **Friday (جمعه)**.
- Formats must follow natural Persian syntax (e.g., `شنبه ۲۴ آبان ۱۴۰۳`).
- Store all timestamps in standard UTC ISO-8601 format in PostgreSQL/Supabase, converting exclusively at the UI/presentation boundary.

### 3.4 Numerals
- Render numbers in Persian digits (۰، ۱، ۲، ۳، ۴، ۵، ۶، ۷، ۸، ۹) by default in dates, times, durations, study metrics, and lists.
- Provide a dedicated, centralized utility for Persian number formatting (`toPersianDigits`, `formatPersianCurrency`, `formatPersianDuration`).

### 3.5 Code Identifiers & Documentation
- All code identifiers (variables, functions, interfaces, database columns, filenames, commits) must remain in **English**.
- In-code technical comments should be in English.
- Technical documentation may be written in Persian while preserving standard English technical terms (e.g., RLS, Server Components, Schema, Middleware).

---

## 4. Technology Stack & Technical Constraints

The project relies on a modern, strictly typed, scalable web stack:

### Frontend
- **Framework:** Next.js (App Router, Server Components & Server Actions where appropriate).
- **Core Library:** React (Functional components, custom hooks, strict React rules).
- **Language:** TypeScript with strict type checking (`strict: true`, `noImplicitAny: true`).
- **Styling:** Tailwind CSS with CSS Variables for theming.
- **Component Primitives:** shadcn/ui (customized for RTL layout and Persian aesthetics).
- **Icons:** Lucide React (configured for RTL contexts).
- **Data Visualization:** Recharts (RTL-aware tooltips, Persian numbers, accessible palette).

### Backend, Database & Auth
- **Database:** Supabase (PostgreSQL 15+).
- **Authentication:** Supabase Auth (Private access, cookie-based session management).
- **SSR Integration:** `@supabase/ssr` with Next.js middleware and server-side client patterns.
- **Data Validation:** Zod schemas for all forms, API payloads, Server Actions, and database inputs.

### Deployment & CI
- **Hosting:** Vercel.
- **Source Control:** GitHub with branch protection, conventional commits, and automated checks.

### Dependency Governance
- Only install well-maintained, stable, and mutually compatible packages.
- Always check documentation before introducing or updating any library.
- Avoid duplicate or overlapping utility libraries.

---

## 5. Database Architecture & Security Standards

### 5.1 Cloud Database Authority
- Supabase PostgreSQL is the sole source of truth for persistent user data.
- Browser `localStorage` or `sessionStorage` must **never** be used as the primary database. It may only store non-sensitive client preferences (e.g., local UI collapsed state).

### 5.2 Private Access & Authentication
- The application is private: **no open public registration**.
- Access is restricted to authenticated users (e.g., invite-only, admin-provisioned, or private credential sign-in).
- Sessions are managed securely via HTTP-only, secure cookies handled by `@supabase/ssr`.

### 5.3 Row Level Security (RLS)
- Every table exposed to the application **must** have RLS enabled:
  ```sql
  ALTER TABLE <table_name> ENABLE ROW LEVEL SECURITY;
  ```
- Strict user ownership validation must be enforced using `auth.uid()`:
  - **SELECT Policy:** Verify `user_id = auth.uid()`.
  - **INSERT Policy:** Enforce `WITH CHECK (user_id = auth.uid())`.
  - **UPDATE Policy:** Enforce both `USING (user_id = auth.uid())` and `WITH CHECK (user_id = auth.uid())`.
  - **DELETE Policy:** Verify `user_id = auth.uid()`.

### 5.4 Secret Management
- Never expose `SUPABASE_SERVICE_ROLE_KEY` or database connection passwords in client-side code or public repositories.
- Only expose `NEXT_PUBLIC_SUPABASE_URL` and `NEXT_PUBLIC_SUPABASE_ANON_KEY` to the browser.
- Store sensitive configuration in `.env.local` and configure them in Vercel environment variables.
- Keep `.gitignore` updated to strictly exclude environment files, build artifacts, and secrets.

### 5.5 Migration Discipline
- All database schema updates, RLS policies, functions, and triggers must be stored in version-controlled SQL migration files within the repository.

---

## 6. Visual Design System: "Premium Minimal Productivity Interface"

Visual excellence is a top-tier project requirement. The interface must feel like a commercial-grade, high-end productivity tool.

### 6.1 Aesthetic Characteristics
- **Sophisticated Minimalism:** Clean surfaces, uncluttered information density, and focused visual anchors.
- **Rigorously Structured Hierarchy:** Clear visual differentiation between page titles, section headers, metric cards, and secondary data.
- **Subtle Depth:** Restrained use of micro-borders (`border-border/60`), subtle background fills, and soft elevation shadows (`shadow-sm`, `shadow-md`).
- **Balanced Whitespace:** Consistent spacing system based on a 4px/8px modular scale.
- **Theme Support:** Native Light Mode and Dark Mode with smooth color transitions and sufficient contrast ratios (WCAG AA compliant).
- **Purposeful Micro-Interactions:** Smooth CSS transitions (150ms-250ms), elegant hover states, visible focus-visible rings for keyboard accessibility.

### 6.2 Anti-Patterns (Strictly Forbidden)
- Generic admin templates with brightly colored cards and crowded sidebars.
- Emojis used as system UI icons (use Lucide React icons instead).
- Excessive, flashy gradients or decorative animations that distract from productivity.
- Oversized empty regions or uneven, arbitrary padding/margin values.
- Mismatched typography sizes or unaligned RTL text blocks.

### 6.3 Comprehensive UI State Patterns
Every screen and data component must gracefully handle all five core UI states:
1. **Loading State:** Skeleton loaders matching the exact shape of the expected content with RTL-compatible shimmer.
2. **Empty State:** Clean Persian illustration/iconography, friendly Persian explanation, and a clear call-to-action button.
3. **Error State:** Human-readable Persian error message with a non-destructive retry mechanism.
4. **Success Feedback:** Unobtrusive Persian toast notifications or inline confirmations.
5. **Confirmation Dialogs:** Modal confirmation dialogs for destructive actions (e.g., deleting study plans or reset actions).

---

## 7. User Experience & Core Study Management Workflows

1. **Frictionless Onboarding & Daily Dashboard:**
   - On opening the app, the user immediately sees today's agenda, current focus tasks, daily study target progress, and overdue items.
2. **Rapid Plan Creation:**
   - Adding subjects, chapters, study goals, or sessions must require minimum clicks with intelligent defaults.
3. **Streamlined Session Logging:**
   - Quick logging of completed study intervals (subject, duration, notes, completion status).
4. **Distraction-Free Timer Mode:**
   - Integrated Pomodoro / Stopwatch timer with clean Persian countdown, pause/resume, and direct session save.
5. **Instant Analytics:**
   - Clean, glanceable charts (daily hours, subject distribution, weekly adherence) without navigating through deep nested menus.

---

## 8. Development & Implementation Rules

### 8.1 Pre-Implementation Phase Checklist
Before writing code for any development phase:
1. Read `GEMINI.md`.
2. Read the relevant technical documentation in the repository.
3. Inspect existing files, components, and schema.
4. Verify existing dependencies and avoid introducing redundant packages.
5. Identify potential regressions or side effects.

### 8.2 In-Implementation Phase Standards
- Write modular, single-responsibility components under `src/components/`.
- Isolate reusable logic into custom hooks (`src/hooks/`) and utility functions (`src/lib/` or `src/utils/`).
- Validate all incoming user inputs with Zod schemas.
- Ensure strict TypeScript typing with zero use of `any`.
- Never delete or alter existing working features unless explicitly instructed.
- When an architectural adjustment is strictly necessary, document the rationale in `DECISIONS.md`.

### 8.3 Post-Implementation Phase Checklist (Quality Control)
After implementing changes in any phase:
1. **TypeScript Check:** Run `tsc --noEmit` and confirm zero type errors.
2. **Linting Check:** Run `npm run lint` and resolve all warnings and errors.
3. **Build Check:** Run `npm run build` to verify production compilation.
4. **Responsive UI Review:** Test at desktop (1440px+), tablet (768px-1024px), and mobile (375px-430px).
5. **RTL & Typography Inspection:** Verify alignment, padding, margin, font rendering, and Persian numerals.
6. **Documentation Update:** Update all affected documentation files.
7. **Git Commit:** Commit changes using conventional commit conventions.

---

## 9. Version Control & Git Discipline

- **Branches:** Use clean, descriptive branch names (e.g., `feature/jalali-calendar`, `fix/rls-session-policy`).
- **Commit Messages:** Follow the Conventional Commits specification:
  - `feat: ...` for new features
  - `fix: ...` for bug fixes
  - `docs: ...` for documentation additions or revisions
  - `style: ...` for formatting, design system, and visual refinements
  - `refactor: ...` for code refactoring without behavior change
  - `test: ...` for adding or updating tests
  - `chore: ...` for tooling, configuration, and dependencies
- **Integrity:** Never claim git commit or push success without explicit command verification.

---

## 10. Required Project Documentation Suite

The project maintains a structured documentation repository. The following documents must be kept accurate and up to date throughout the project lifecycle:

| Document | Purpose & Scope |
| :--- | :--- |
| `README.md` | Project overview, quickstart, environment setup, and deployment guide. |
| `GEMINI.md` | Permanent architectural, visual, engineering, and operational instructions. |
| `PROJECT_OVERVIEW.md` | Vision, target audience, core objectives, and high-level product scope. |
| `PRODUCT_REQUIREMENTS.md` | Detailed functional and non-functional specifications. |
| `DATABASE_SCHEMA.md` | PostgreSQL database schemas, relations, indexes, RLS policies, and migrations. |
| `UI_UX_SPECIFICATION.md` | Detailed UI behaviors, layouts, responsive rules, Persian typography, and RTL standards. |
| `DESIGN_SYSTEM.md` | Color tokens, typography scales, spacing scale, component library guidelines, and themes. |
| `TECHNICAL_ARCHITECTURE.md` | Full-stack architecture, Next.js App Router patterns, Supabase integration, and state strategy. |
| `FEATURES_AND_ACCEPTANCE_CRITERIA.md` | Granular feature descriptions with explicit acceptance test criteria. |
| `DEVELOPMENT_ROADMAP.md` | Phased breakdown of milestones, implementation stages, and progress tracking. |
| `SECURITY_AND_PRIVACY.md` | Auth flows, RLS rules, data sanitization, privacy standards, and threat model. |
| `TEST_PLAN.md` | QA strategy, unit tests, integration tests, E2E tests, and responsive/RTL checklists. |
| `DECISIONS.md` | Architectural Decision Records (ADR) documenting key choices and trade-offs. |
| `CHANGELOG.md` | Chronological record of completed phases, features, fixes, and updates. |

---

## 11. Current Execution Status

- **Phase:** Phase 0 - Project Foundation & Instruction Setup
- **Status:** Permanent instruction file `GEMINI.md` established.
- **Next Step:** Await explicit instruction from the user for the next development phase.
