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

Guiding principle: **build with tech, think in business, move like a hustler.**

## Repository truth

This document must describe the repository as it actually exists. Never claim that an application, test suite, CI pipeline, payment integration, security control, or deployment exists unless it has been verified.

At the time of this revision, the repository contains documentation and the initial README; application source code has not yet been verified in the repository.

## Engineering principles

### 1. Production quality by default

Treat implementation work as production code unless explicitly marked as a prototype.

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

### 2. Simplest robust solution

Avoid premature abstractions, unnecessary dependencies, speculative architecture, and over-engineering.

Choose the smallest reliable implementation that can evolve when real requirements appear.

### 3. Inspect before modifying

Before changing a file:

1. Read the relevant existing code and configuration.
2. Identify current conventions and dependencies.
3. Check whether the requested behavior already exists.
4. Make the smallest coherent change.
5. Review the resulting diff for unrelated modifications.

Never overwrite working behavior without understanding it first.

### 4. Secrets and trust boundaries

Never commit or expose:

- API keys
- Access tokens
- Private keys
- Payment secrets
- Webhook secrets
- Database credentials
- OAuth client secrets
- Personal access tokens
- Real `.env` files

Use environment variables. Provide variable names, never real values, in `.env.example`.

Treat browser input, third-party responses, webhooks, and uploaded files as untrusted until validated server-side.

### 5. Client/server boundaries

Privileged operations and secrets must remain server-side.

Examples:

- FedaPay secret credentials
- Webhook verification
- Database credentials
- Admin operations
- Third-party API secrets

Never use client-side state, hidden fields, redirects, or UI messages as proof of authorization or payment.

### 6. Payments

For FedaPay or another payment provider:

1. Create/initiate transactions safely.
2. Keep secret credentials server-side.
3. Determine or validate product, currency, amount and order state server-side.
4. Verify payment status server-side.
5. Authenticate provider webhooks according to current official documentation.
6. Make fulfillment idempotent.
7. Never unlock a paid product solely because the browser reached a success URL.
8. Never trust transaction values supplied by the client.
9. Prevent replay or duplicate fulfillment.

### 7. Input validation

Validate external data at system boundaries:

- Form fields
- URL parameters
- Query parameters
- API payloads
- Webhook payloads
- Uploaded files
- External API responses

Validate type, shape, size, format and allowed values where known. Reject invalid input safely.

### 8. Accessibility

Use:

- Semantic HTML
- Keyboard navigation
- Visible focus states
- Accessible labels
- Sufficient contrast
- Meaningful link text
- Appropriate alt text
- Reduced-motion support

Do not make essential information or interactions dependent on animation.

### 9. Performance

The site should feel premium without becoming heavy.

Prefer:

- Optimized responsive images
- Appropriate lazy loading
- Minimal client-side JavaScript
- Server rendering where useful
- Small dependency footprint
- CSS transforms/opacity for animation
- Stable layouts that minimize CLS
- Avoiding unnecessary hydration

Measure before introducing complex performance optimizations.

### 10. Motion and visual design

Visual direction:

**Tech × Editorial × Cinematic × Human**

Motion should support hierarchy, storytelling and perceived quality. Avoid generic cyberpunk aesthetics, excessive neon, gratuitous 3D, and motion that harms usability or performance.

Respect `prefers-reduced-motion` and ensure animated states have usable non-animated equivalents.

### 11. SEO and sharing

Every public page should have deliberate:

- Title
- Meta description
- Canonical URL where appropriate
- Open Graph metadata
- X/Twitter card metadata where appropriate
- Semantic heading hierarchy
- Descriptive URLs

Do not use misleading metadata, hidden text, or keyword stuffing.

### 12. Content integrity

Never invent:

- Client results
- Revenue figures
- Testimonials
- Partnerships
- Credentials
- Certifications
- User counts
- Product capabilities

If a claim is not verified, mark it as unknown or request its source.

### 13. Components and code organization

Prefer small components with one clear responsibility.

Avoid:

- Giant page components
- Repeated magic numbers
- Deep coupling
- Unnecessary global state
- Duplicate business logic

Keep business logic separate from presentation when that improves testability and clarity.

### 14. TypeScript

When TypeScript is used:

- Prefer strict typing.
- Avoid `any` unless justified at a documented boundary.
- Type external data at the boundary.
- Prefer discriminated unions for finite states.
- Avoid assertions that hide runtime uncertainty.
- Keep public types intentional and minimal.

### 15. Error handling and observability

Errors must be explicit and actionable.

Do not silently swallow exceptions. User-facing errors should be safe and understandable. Server diagnostics must not expose secrets or unnecessary personal data.

Use structured logging where appropriate and avoid logging credentials, payment secrets, tokens or sensitive personal data.

### 16. Dependencies and supply chain

Before adding a dependency, verify:

- It solves a real requirement.
- It is maintained or otherwise justified.
- Its license is compatible with the project.
- Its security and transitive dependency risk is acceptable.
- The platform or existing stack cannot reasonably provide the same capability.

Prefer lockfiles and reproducible installs. Avoid arbitrary remote scripts and unpinned CI dependencies when practical.

## Required validation before push

Run the checks that actually exist in the repository. Prefer, when configured:

- Formatting
- Linting
- Type checking
- Unit/integration tests
- Production build
- Relevant security/dependency checks

Never report a check as passed unless it was actually run. If a check is unavailable, state that it was not run rather than implying success.

## Four-pass review protocol

Every meaningful change should be reviewed from four different perspectives before it is considered ready.

### Pass 1 — Functional correctness

Ask:

- Does the implementation satisfy the requirement?
- Are edge cases handled?
- Are loading, success and failure states coherent?
- Are existing behaviors preserved?

### Pass 2 — Security and trust boundaries

Ask:

- Can untrusted input cross a boundary without validation?
- Can a client influence authorization, price, ownership or payment state?
- Are secrets exposed in source, logs, client bundles or errors?
- Are webhooks authenticated and idempotent?
- Are dependencies and integrations reasonably controlled?

### Pass 3 — Quality and resilience

Ask:

- Is the code type-safe and maintainable?
- Does it behave on mobile and desktop?
- Is it accessible by keyboard and assistive technology?
- Is the performance cost justified?
- Are errors observable without leaking sensitive information?
- Is the implementation unnecessarily complex?

### Pass 4 — Adversarial and product review

Assume the implementation will be used incorrectly or attacked.

Ask:

- What happens with malformed input?
- What happens if an API is unavailable?
- What happens if a request is repeated?
- What happens if a user bypasses the UI?
- What happens under slow networks or small screens?
- Does the implementation actually improve the user's journey?
- Does the diff contain unrelated work?

Only after all four passes should the change be considered ready.

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

A change is not complete merely because it works visually. Review it across:

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

Then perform a final simplification pass: remove anything that does not provide measurable value.

## Definition of done

A feature is done when:

- Requested behavior is implemented.
- Mobile and desktop behavior is considered.
- No obvious accessibility regression remains.
- No known secret exposure remains.
- External input is validated.
- Sensitive operations are authorized server-side.
- Errors are handled safely.
- Relevant checks pass, or their absence is explicitly documented.
- Production build succeeds when a build exists.
- Non-obvious behavior is documented.
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
- Never hide failed checks or known limitations.
