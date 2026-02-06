# UI Bugs Report

## 1. Flaky Navigation Links in Home Page

**Severity**: Medium
**Location**: `frontend/src/app/page.tsx`, `frontend/src/components/TruncatedText.tsx`

**Description**:
The "New Chapters" and "New Projects" links on the Home page are unreliable, causing intermittent failures in automated E2E tests and potentially affecting user experience.

**Root Cause**:
The `Link` component (Next.js) renders an `<a>` tag, which has `display: inline` by default (or via Tailwind reset). Inside this `Link`, the `TruncatedText` component renders a `<span>` with the `block` class (`display: block`).
While HTML5 allows block elements inside anchor tags, the mixing of display modes (inline parent containing block child) without explicit styling on the parent can lead to fragmented hit-testing areas, making the link difficult to click or non-responsive in certain parts.

**Recommendation**:
Add `block` or `inline-block` to the `className` of the `Link` components wrapping `TruncatedText`.

## 2. Malformed Global Error Boundary

**Severity**: High
**Location**: `frontend/src/app/global-error.tsx`

**Description**:
The global error boundary (`GlobalError` component) replaces the root layout when a critical error occurs. However, it fails to include the required `<html>` and `<body>` tags.

**Root Cause**:
Next.js documentation specifies that `global-error.js` (or `.tsx`) must define its own `<html>` and `<body>` tags because it replaces the entire root layout. The current implementation returns an `<ErrorDisplay>` component which only contains a `<div>`. This results in a malformed DOM structure when the global error boundary is active.

**Recommendation**:
Wrap the return value of `GlobalError` in `<html>` and `<body>` tags.

## 3. Missing Environment Variable Configuration

**Severity**: Medium
**Location**: `frontend/src/utils/env.client.ts`, `frontend/src/sentry.server.config.ts`

**Description**:
The application logs repeated errors related to Sentry configuration: `TypeError: An error occurred while loading instrumentation hook: Cannot read properties of undefined (reading 'toLowerCase')`.

**Root Cause**:
The code attempts to access `ENVIRONMENT.toLowerCase()` in `sentry.server.config.ts`, but `ENVIRONMENT` (sourced from `NEXT_PUBLIC_ENVIRONMENT`) is undefined in the default environment because `.env` is missing (only `.env.example` exists).

**Recommendation**:
Ensure `NEXT_PUBLIC_ENVIRONMENT` is properly defined in the environment or handle the `undefined` case in the code.
