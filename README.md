# Staffing Agreement Readiness Assistant

A working staffing-operations prototype by **Abhijnan Chaturvedi**. Compare a confirmed order with its draft agreement, identify commercial discrepancies with source evidence, record corrections and exceptions, and prepare a clarification before approval.

## Try the product

After GitHub Pages is enabled, use the published Pages link in this repository's About section. The repository contains the source; the Pages address opens the actual app.

1. Select **Rate mismatch**, **Missing terms**, or **Aligned agreement**.
2. Choose **Compare documents**.
3. Inspect **Evidence** on a term. Correct a value or record a justified mismatch exception.
4. Review the clarification and export the session as Markdown or JSON.
5. Edit the source text and compare again to test a different scenario.

Sample organizations and documents are fictional. No login or paid service is needed for structured text review.

## Problem, solution, and value

Staffing orders and draft agreements can disagree on bill rates, dates, hours, payment terms, or contracting entities. The assistant brings the comparison and its supporting passages into one workflow. The intended outcome is less review effort and fewer avoidable commercial errors.

The product includes an editable value calculator. Its inputs are assumptions, not measured customer results. See the [product blueprint](docs/blueprint.md) for workflow, scope, evaluation strategy, and value realization.

## What is implemented

- Editable documents, UTF-8 `.txt` upload, and three synthetic cases.
- Local extraction of supported labeled fields, with original source lines.
- Eleven configured comparison checks, including currency and rate-basis handling.
- Explicit missing/ambiguous/unsupported states, invalid-date checks, and protection against false-clear outcomes covered by the test cases.
- Evidence inspection, user-attributed corrections, accepted mismatch exceptions, reopening, and automatic rechecking.
- Editable clarification draft, clipboard action, and current-session Markdown/JSON exports.
- Responsive interface, keyboard-accessible controls, and a configurable value calculator.
- An optional Cloudflare Workers AI endpoint with input limits, evidence validation, CORS restrictions, an approximate rate limit, bounded output, and explicit failures.

## AI status: important distinction

**Structured text review works immediately and uses local rules, not a language model.** It reads labeled fields such as `Client:`, `Start date:`, and `Client bill rate:`. It is not a general document understanding engine.

**Live AI extraction requires a Cloudflare account and a deployed endpoint.** `config.js` is deliberately empty until that endpoint exists. AI behavior has been tested with mocked provider responses; no real model accuracy or live deployment is claimed. The app never silently substitutes sample results for failed AI calls. See [setup](docs/SETUP.md) and [validation](docs/VALIDATION.md).

## Architecture

```text
GitHub Pages: static HTML + CSS + JavaScript
  -> Local labeled-field extraction OR optional Cloudflare AI endpoint
  -> Structured fields + verified source references
  -> Explicit comparison rules
  -> Human review, corrections, and exceptions
  -> Clarification and session export
```

The implementation uses standard JavaScript modules with no frontend build step or runtime package dependencies. This simplifies free GitHub Pages publishing. The original blueprint suggested React/TypeScript; the prototype uses a smaller buildless implementation to reduce setup and hosting friction.

Documents and review state remain in browser memory in structured mode. Live AI transmits both texts to Cloudflare only after an explicit user action. No app database, raw document logs, automatic sending, or stored uploads are included. Refreshing clears the session. Exports contain document text and review notes, so users decide where to retain them.

## Run locally

From this directory, run a static server:

```sh
python3 -m http.server 8080
```

Then open `http://localhost:8080`. Opening `index.html` directly using a `file:` URL may prevent JavaScript modules from loading. No package installation is needed.

## Run automated checks

Install a supported Node.js version, then:

```sh
node --test tests/*.test.js
```

The suite covers extraction/comparison edge cases, evidence validation, API validation, CORS, rate-limit errors, bounded retry, and provider failures. These checks do not establish accuracy on real agreements or replace a production security review.

## Limits and next steps

English text only; no PDF/OCR. Document precedence is selected by the user; amendments and master agreements need separate review. The app checks configured commercial fields, not legal enforceability or compliance. A clear status does not grant approval or a signature. Session exports are not tamper-proof audit logs.

Next: connect and evaluate live AI on at least 12 labeled pairs with repeated runs, interview staffing practitioners, measure active review time, and validate whether the assistant reduces effort without increasing missed discrepancies. Only then expand document formats or integrate signature routing.

## Free hosting

GitHub Pages hosts the static product from a public repository on GitHub Free. Optional inference is a separate Cloudflare Workers Free deployment subject to its model eligibility and daily quotas. Keep paid plans and paid add-ons disabled to preserve the zero-cost boundary. See [official GitHub Pages documentation](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages) and [Cloudflare AI pricing](https://developers.cloudflare.com/workers-ai/platform/pricing/).
