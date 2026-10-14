# Core Banking Platform — Frontend User

`frontend-user` is the **customer-facing web application repository** for the Core Banking Platform. It provides secure, responsive internet banking experiences for **individual** and **corporate** customers through approved backend APIs.

> **Development status:** This project is being developed by a volunteer team. It is **not production-ready** and must not be used to process real customer funds or sensitive banking data until the required security, regulatory, and operational approvals have been completed.

## Repository separation

The frontend platform is divided into three independently maintained repositories:

| Repository | Responsibility |
| --- | --- |
| **`frontend-user`** (this repository) | Individual and corporate internet banking **web portals** |
| `frontend-admin` | Internal staff operations, platform administration, risk/compliance, finance, reconciliation, and reporting web portals |
| `frontend-mobile` | Customer mobile banking and agent banking applications (Flutter/Dart) |

The repositories share **API contracts, design standards, security requirements, and user experience conventions**, but must not assume direct imports from each other's source trees. Reusable assets and clients may be distributed through separately versioned packages, where approved.

## Objectives

- Make everyday banking clear, accessible, secure, and reliable for customers.
- Support individual and corporate customer journeys without mixing authorisation boundaries.
- Respect tenant/institution branding, entitlements, and available products.
- Support desktop, tablet, and mobile web browsers.
- Integrate with backend services using typed, approved API contracts.
- Provide consistent loading, validation, failure, confirmation, and transaction-result experiences.
- Enable automated testing, code review, and controlled deployment.

## Applications in scope

### 1. Individual internet banking

**Audience:** Personal banking customers.

**Planned capabilities:**

- Registration, login, multi-factor authentication, password recovery, and security settings
- Account overview, available/ledger balances, and transaction history
- Internal and interbank transfers, beneficiaries, and scheduled transfers
- Bill payments, airtime, mobile data, electricity, and cable TV payments
- Statements, receipts, card controls, and transaction status tracking
- Loan applications, savings goals, and profile management
- Notifications, support requests, and secure messaging

### 2. Corporate internet banking

**Audience:** Business owners, finance managers, accountants, payroll officers, corporate administrators, payment initiators, and approvers.

**Planned capabilities:**

- Multi-account and multi-company account views, where authorised
- Corporate user administration, roles, mandates, and transaction limits
- Maker-checker payment initiation and approval workflows
- Bulk payments, payroll, payment file upload, and payment templates
- Beneficiaries, scheduled payments, virtual accounts, and collections
- Cash pooling, account sweeping, and escrow account access, where supported
- Statements, reconciliation file download, and approval queues
- Authorised API credentials, host-to-host configuration, and support requests

**Important:** Features listed above describe the intended product scope, **not a claim that each feature has already been implemented**. Delivery is tracked in issues and milestones.

## Out of scope

This repository must not contain internal teller workflows, branch operations, institution/system administration, AML/fraud case management, general-ledger reporting, or native Flutter apps. These belong in `frontend-admin` or `frontend-mobile` as appropriate. Backend banking logic, ledger posting, fee calculation, and final authorisation decisions remain in backend services.

## Proposed technology stack

| Area | Recommended choice |
| --- | --- |
| Web framework | Next.js / React |
| Language | TypeScript (strict mode) |
| Styling | Tailwind CSS |
| Forms and validation | React Hook Form + Zod |
| Server state | TanStack Query |
| Data tables | TanStack Table, where needed |
| API integration | OpenAPI-generated typed clients, where available |
| Unit/component testing | Jest or Vitest and React Testing Library |
| End-to-end testing | Playwright |
| Component documentation | Storybook, where appropriate |
| Quality tools | ESLint, Prettier, TypeScript |

These are proposed choices based on the platform plan. **The versions and commands in the checked-in project configuration are authoritative.**

## Suggested project structure

A structure suitable for a single repository containing the two web portals is:

```text
frontend-user/
├── apps/
│   ├── individual-banking/        # Personal customer web portal
│   └── corporate-banking/         # Corporate customer web portal
├── packages/
│   ├── ui/                        # Reusable customer-facing components
│   ├── design-tokens/             # Shared styles and theme contracts
│   ├── api-client/                # Generated/typed backend clients
│   ├── auth/                      # Authentication integration
│   ├── permissions/               # UI entitlement helpers
│   ├── forms/                     # Shared form components
│   ├── validation/                # Shared input schemas
│   ├── types/                     # Common TypeScript types
│   └── utils/                     # Formatting and other utilities
├── docs/
│   ├── architecture/
│   ├── authentication/
│   ├── accessibility/
│   ├── api-integration/
│   ├── design-system/
│   └── deployment/
├── .github/workflows/
├── .env.example
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── SECURITY.md
├── LICENSE
└── README.md
```

> This is a **target layout** rather than a statement that these directories already exist. Shared modules should be extracted only where there is a real reuse requirement. Application-specific workflows should stay inside their respective apps.

## Local development

### Prerequisites

- Git
- A supported Node.js LTS version
- The package manager and version specified by the repository lockfile / `packageManager` field
- Access to a development backend or approved mocked API
- An editor such as VS Code

### Set up

```bash
# Use the actual repository URL provided by the maintainers
git clone <frontend-user-repository-url>
cd frontend-user

# If pnpm is the configured package manager
corepack enable
pnpm install

# Create your local environment configuration
cp .env.example .env.local
```

Configure `.env.local` with approved **development-only** endpoints. On Windows PowerShell, use `Copy-Item .env.example .env.local` instead of `cp` if desired.

### Run the applications

If the repository adopts the proposed pnpm workspace and application package names:

```bash
pnpm --filter individual-banking dev
pnpm --filter corporate-banking dev
```

Actual package names, port assignments, and scripts must be confirmed in each application's `package.json`. Example URLs could be `http://localhost:3000` and `http://localhost:3001`; they are **not guaranteed defaults**.

### Quality checks

Run the scripts supported by the project configuration. For a workspace with the corresponding scripts:

```bash
pnpm lint
pnpm test
pnpm build
```

Do not switch package managers or commit additional lockfiles without maintainer approval.

## Environment configuration

A safe `.env.example` may contain:

```dotenv
NEXT_PUBLIC_APP_NAME=Core Banking Platform
NEXT_PUBLIC_API_BASE_URL=http://localhost:5000
NEXT_PUBLIC_IDENTITY_URL=http://localhost:8080
NEXT_PUBLIC_ENVIRONMENT=development
```

The values above are **illustrative**, not verified deployed endpoints. `NEXT_PUBLIC_` variables are visible to the browser and must **never** contain secrets, API keys, client secrets, or private credentials. Keep real `.env.local` files out of version control. Use only an approved authentication/session design.

## Authentication, authorisation, and security

- Use approved backend identity services for login, MFA, recovery, and session lifecycle.
- Enforce customer and corporate entitlements in backend APIs; hiding a button is **not** an authorisation control.
- Scope all data and actions to the authenticated customer, corporate entity, and institution.
- Design secure session expiry, reauthentication, lockout, and logout flows.
- Avoid storing tokens or sensitive banking data in browser storage unless explicitly approved after security review. Prefer appropriately protected server-managed sessions where the architecture permits.
- Protect against XSS, CSRF, open redirects, clickjacking, and accidental disclosure.
- Never log passwords, OTPs, PINs, full card details, BVNs, NINs, access tokens, or real customer documents.
- Never embed backend secrets or connect directly to a banking database from the web app.
- Use synthetic test customers and synthetic transaction records in development.
- Report suspected vulnerabilities privately through `SECURITY.md`, not public issues.

## API integration principles

Backend systems are the **source of truth** for balances, fees, limits, posting results, and transaction status. Use approved, versioned APIs and typed request/response models.

- Handle authentication failures, validation errors, timeouts, unavailable services, insufficient funds, exceeded limits, restricted accounts, and provider failures.
- Use correlation IDs for safe troubleshooting without logging private data.
- Support request cancellation and bounded timeouts.
- Do not blindly retry **money-moving requests**. Coordinate idempotency keys, request status checks, and retry rules with backend contracts.
- For unknown transaction outcomes, show a **processing/unknown** state and let the customer verify status rather than suggesting immediate resubmission.

## Transaction experience

All payment flows should follow a predictable sequence:

1. Collect and validate details.
2. Present the beneficiary, amount, currency, and any applicable fees.
3. Show a review and confirmation screen.
4. Request the required authentication or corporate approval.
5. Submit once using the agreed idempotency mechanism.
6. Show pending, successful, failed, or unknown status accurately.
7. Display the transaction reference and receipt when the backend provides them.

Disable repeat submission during processing; never mark a payment successful solely because an HTTP request was sent. Display available balance and ledger balance distinctly, and use explicit currency and date formatting.

## Corporate approval flows

Corporate banking must account for delegated authorisation and multi-step workflows:

- A payment **initiator** may prepare a transfer without being authorised to approve it.
- **Approvers** should see the exact amounts, beneficiaries, fees, status, and approval requirements.
- Clearly distinguish draft, submitted, pending approval, rejected, processing, completed, and failed states.
- Display remaining approvals and audit-relevant metadata supplied by approved APIs.
- Do not bypass backend mandates, approval thresholds, or segregation-of-duties rules.

## Design, accessibility, and responsiveness

Use a consistent, reusable design system with components for fields, currency input, tables, pagination, dialogs, notifications, transaction summaries, and confirmation screens. Every user journey should account for loading, empty, validation, error, success, restricted-access, session-expired, and network-failure states.

Aim for **WCAG 2.1 AA** or a later approved standard: keyboard accessibility, visible focus, semantic controls, adequate contrast, labelled inputs, accessible errors, and screen-reader-friendly status updates. Support desktop, tablet, and mobile web, including slow networks. Never communicate transaction status by colour alone.

Institution-specific logos, colours, legal text, currencies, time zones, locale formats, and enabled features should be configured rather than hard-coded wherever possible.

## Testing strategy

Include tests at several levels:

- **Unit:** currency/date formatters, validators, permissions, and pure utilities.
- **Component:** forms, navigation, account cards, transaction review, and loading/error states.
- **Integration:** identity flows, API errors, entitlement behaviour, and multi-step approval workflows.
- **End-to-end:** individual login, onboarding, account views, transfers, bill payments, corporate payment initiation, and corporate approval.
- **Accessibility/responsive:** keyboard navigation, screen readers, narrow displays, and error focus management.

For financial journeys, test insufficient balance, limit breaches, restricted accounts, duplicate clicks, expired sessions, rejected approvals, provider timeouts, network loss, reversal outcomes, and unknown transaction status.

## Contribution workflow

This project currently welcomes volunteer collaboration. Contributors should read `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, and `SECURITY.md` if present before submitting changes.

1. Pick or obtain assignment to an issue with acceptance criteria.
2. Create a short-lived branch; do not commit directly to `main`.
3. Implement the smallest safe change, including accessibility and failure states.
4. Run the available lint, type-check, test, and build scripts.
5. Open a pull request linked to the issue, with screenshots for UI changes and notes on API/security impacts.
6. Obtain review and pass required CI checks before merging.

Suggested branch names:

```text
feature/TASK-0301-account-dashboard
fix/TASK-0302-transfer-confirmation
test/TASK-0303-corporate-approvals
docs/TASK-0304-local-setup
```

Suggested commits:

```text
feat: add individual account dashboard
fix: prevent duplicate payment submissions
test: cover corporate approval rejection
docs: clarify development configuration
```

## Definition of done

A feature is complete when its acceptance criteria are met; the interface is responsive and accessible; relevant loading, error, empty, and authorisation states are covered; validation and backend integration work correctly; important financial edge cases are tested; sensitive data is handled safely; documentation is updated; and required reviews and checks have passed.

## Volunteer participation and licence

This is presently an **unpaid volunteer project**. Participation does not create employment or guarantee compensation. Contributions must comply with the repository's `LICENSE` and must not incorporate third-party proprietary code, designs, or assets without permission. Consult `LICENSE` for the actual legal terms; do not assume a particular open-source licence.

## Production-use disclaimer

The project is intended for development, learning, research, and collaborative engineering. It must **not** be deployed for real banking transactions or real customer financial data without formal security assessment, penetration testing, legal/regulatory review, performance and accessibility testing, operational monitoring, disaster-recovery validation, and explicit production-readiness approval.

## Getting help

Use the team's approved issue tracker or communication channel. Include the app name (`individual-banking` or `corporate-banking`), task ID, steps to reproduce, expected/actual behaviour, browser, and non-sensitive logs or screenshots. Never post credentials, tokens, customer records, or bank-account details.

---

Thanks to everyone contributing engineering, design, QA, security, and documentation work to the Core Banking Platform.
