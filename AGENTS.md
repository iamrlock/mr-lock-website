# AGENTS.md — MR LOCK Website

## Mission

This repository contains the public website for **MR LOCK / Jean Marc Ahouansou**.

The website is a business asset, not a generic developer portfolio. It should communicate:

- Tech credibility
- Entrepreneurial execution
- AI and automation expertise
- Business and sales thinking
- E-sports / gaming DNA
- Web3 experimentation
- Digital products and e-commerce
- A premium, editorial, futuristic and human brand

The guiding principle is: **build with tech, think in business, move like a hustler.**

## Current repository state

At the time this file was created, the repository contains the initial project README and does not yet contain the application source code. Do not pretend that an implementation exists when it does not.

## Engineering principles

### 1. Production quality by default

Every implementation must be treated as production code unless explicitly marked as a prototype.

Prioritize:

- Correctness
- Security
- Accessibility
- Performance
- Maintainability
- Responsive behavior
- SEO
- Clear error handling
- Small, composable abstractions

Do not add complexity merely to appear sophisticated.

### 2. Prefer the simplest robust solution

Avoid premature abstractions, unnecessary dependencies, speculative architecture, and over-engineering.

A feature should be implemented with the smallest reliable surface area that can scale when real requirements appear.

### 3. Never expose secrets

Never commit:

- API keys
- Access tokens
- Private keys
- Payment secrets
- Database credentials
- Webhook secrets
- OAuth client secrets
- `.env` files containing secrets
- Personal access tokens

Use environment variables and document required variable names in `.env.example` without real values.

### 4. Client/server boundaries must be explicit

Secrets and privileged operations must remain server-side.

Examples:

- FedaPay secret credentials: server only
- Webhook verification: server only
- Database credentials: server only
- Admin operations: server only
- Third-party API secrets: server only

Never rely on client-side redirects or UI state as proof of a successful payment.

### 5. Validate external input

Treat all user input and third-party payloads as untrusted.

Validate:

- Form fields
- URL parameters
- Query parameters
- API payloads
- Webhook payloads
- Uploaded files
- External API responses

Use strict schemas where practical and fail safely.

### 6. Payments

For any future FedaPay integration:

1. Create/initiate the transaction safely.
2. Keep secret credentials on the server.
3. Verify payment status server-side.
4. Validate webhook authenticity/signature according to the provider documentation.
5. Make fulfillment idempotent.
6. Never unlock a paid product solely because a browser returned to a success URL.
7. Never trust an amount, currency, transaction status, or product identifier supplied by the client.

### 7. Accessibility

The website must remain usable without relying exclusively on visual effects.

Use:

- Semantic HTML
- Keyboard navigation
- Visible focus states
- Accessible labels
- Sufficient contrast
- Meaningful link text
- Alt text for informative images
- Reduced-motion support where appropriate

Do not use animation to communicate information that cannot otherwise be accessed.

### 8. Performance

The site is intended to feel premium without becoming heavy.

Prefer:

- Optimized images
- Responsive image sizes
- Lazy loading when appropriate
- Minimal JavaScript for static content
- Server rendering where useful
- Small dependency footprint
- CSS transforms/opacity for animation
- Avoiding layout shifts

Do not ship large libraries for functionality that can be implemented simply with the platform.

### 9. Motion and visual design

The visual direction is:

**Tech × Editorial × Cinematic × Human**

Motion should support hierarchy, storytelling and perceived quality. Avoid generic cyberpunk aesthetics, excessive neon, gratuitous 3D and motion that harms usability or performance.

Respect `prefers-reduced-motion`.

### 10. SEO and sharing

Every public page should have deliberate:

- Title
- Meta description
- Canonical URL where appropriate
- Open Graph metadata
- Twitter/X card metadata where appropriate
- Semantic heading hierarchy
- Descriptive URLs

Do not generate misleading metadata or keyword spam.

### 11. Content integrity

Do not invent:

- Client results
- Revenue figures
- Testimonials
- Partnerships
- Credentials
- Product capabilities
- Certifications
- User counts

If information is not verified, mark it as unknown or ask for the source.

### 12. Components and code organization

Prefer small components with one clear responsibility.

Avoid:

- Giant page components
- Repeated magic numbers
- Deeply coupled components
- Unnecessary global state
- Duplicate business logic

Keep business logic separate from presentation when that improves testability and clarity.

### 13. TypeScript

When TypeScript is used:

- Prefer strict typing.
- Avoid `any` unless there is a documented reason.
- Type external data at the boundary.
- Prefer discriminated unions for finite states.
- Avoid type assertions that hide runtime uncertainty.

### 14. Error handling

Errors must be explicit and actionable.

Do not silently swallow exceptions.

User-facing errors should be safe and understandable. Server logs may contain diagnostic context but must not expose secrets or unnecessary personal data.

### 15. Dependencies

Before adding a dependency, ask:

- Is it necessary?
- Is the package maintained?
- Does it solve a real problem?
- Can the platform or existing stack do this adequately?
- Does it increase bundle size or attack surface significantly?

Do not add a dependency only because it is fashionable.

## Required validation before push

Before pushing application changes, run the project checks that exist in the repository, preferably:

- Formatting
- Linting
- Type checking
- Unit/integration tests
- Production build

If a check cannot be run, state that explicitly in the commit/PR summary.

## Git workflow

Use small, focused commits.

Recommended format:

`type(scope): concise description`

Examples:

- `feat(home): add hero section`
- `feat(products): add product catalog`
- `fix(payment): verify webhook status server-side`
- `perf(images): optimize hero assets`
- `security(forms): harden contact submission`
- `docs(security): document reporting policy`

Never force-push or rewrite shared history unless explicitly requested.

## Review standard: +10 levels

A change is not considered complete merely because it works visually.

Before declaring a feature finished, review it across these dimensions:

1. Correctness
2. Type safety
3. Security
4. Accessibility
5. Responsive behavior
6. Performance
7. SEO
8. Error handling
9. Maintainability
10. User experience

Then perform a final pass for unnecessary complexity and remove anything that does not provide measurable value.

## Definition of done

A feature is done when:

- It satisfies the requested behavior.
- It works on mobile and desktop.
- It has no obvious accessibility regression.
- It has no known secret exposure.
- External input is validated.
- Errors are handled safely.
- Existing checks pass.
- The production build succeeds when applicable.
- The implementation is documented when behavior is non-obvious.
- The diff contains no unrelated changes.

## Agent behavior

Agents working in this repository must:

- Inspect existing code before changing it.
- Preserve working behavior unless the task requires a change.
- Prefer incremental improvements.
- State assumptions when requirements are ambiguous.
- Never claim tests passed unless they were actually run.
- Never claim a security property that was not verified.
- Never commit secrets.
- Never fabricate external integrations.
