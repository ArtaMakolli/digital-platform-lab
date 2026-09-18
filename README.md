# Digital Platform Lab - Updated 2026-09-18 

Digital Platform Lab is the practical workspace for the IHM course **Teknik för digitala plattformar**.

The lab follows data through a small web application: from a user action in the browser, through client-side code and a network request, to server-side handling and a response. The code is kept small enough to inspect, change, and debug during class.

## Scope

The repository will contain:

- a plain HTML, CSS, and JavaScript client
- a small Node.js server
- lesson-specific labs
- student instructions
- troubleshooting notes

Course plans, presentations, assessment instructions, and student submissions remain in itslearning.

## Structure

- `public/` - browser-facing files used by the application
- `labs/` - lesson-specific practical exercises
- `docs/` - guides shared across the course

## Working rules

- Repository documentation and code comments are written in English.
- Student-facing lesson material may be written in Swedish.
- Examples use synthetic data only.
- Do not add personal data, credentials, API keys, or production data.
- Each lab must state what to observe, what to change, and how to verify the result.

## Run the lab

The lab requires Node.js 20 or later. It has no external runtime dependencies.

```bash
npm start
```

Open <http://localhost:3000> and follow the [Lab 1–9 index](labs/README.md). The step-by-step student instructions are in Swedish.

Run the automated checks with:

```bash
npm test
```

## StackBlitz

Open a fresh browser-based copy:

<https://stackblitz.com/fork/github/MatteoDiAmare/digital-platform-lab>

## Status

The pilot application implements one observable client-to-server event flow. Labs 1–9 now provide complete guided instructions using that application; later labs ask students to make small controlled changes to its client code. It is a teaching model, not a production analytics system.
