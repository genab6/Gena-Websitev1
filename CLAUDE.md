# CLAUDE.md — Gena Beauty Portfolio

This file provides guidance to Claude Code when working with this repository.

## Project Overview

A luxury makeup artist portfolio site for Gena Beauty (genabeauty.mua@gmail.com).
Built with React 18, Vite, and EmailJS. Deployed via GitHub Pages.

## Stack

- **Framework**: React 18 (single-page, all in `src/App.jsx`)
- **Styles**: CSS-in-JS (template literals inside JSX)
- **Email**: EmailJS (free tier, 200 emails/month)
- **Instagram**: Behold widget
- **Deploy**: GitHub Pages — push to `main` auto-deploys

## Key Files

- `src/App.jsx` — entire app (components, styles, logic)
- `public/` — static assets (softglam.jpg, index.html)
- `README.md` — project overview

## Git Identity

```
user.name = genab6
user.email = genab6@users.noreply.github.com
```

**Always commit and push after every change.**

## Available Slash Commands

Use these commands when working on this project:

- `/plan` — plan a feature before coding
- `/code-review` — review code quality
- `/build-fix` — fix build/compile errors
- `/security-review` — check for vulnerabilities
- `/refactor-clean` — clean up dead code
- `/tdd` — test-driven development workflow
- `/learn` — extract patterns from the session
- `/checkpoint` — save verification state
- `/verify` — run verification loops

## Active Skills

- `frontend-patterns` — React component patterns, state, performance
- `security-review` — security checklist
- `tdd-workflow` — testing methodology

## EmailJS Config (in src/App.jsx ~line 259)

- Service ID: `service_tc6j74h`
- Notify template (→ Gena): `template_kznrs9e`
- Auto-reply template (→ client): `template_ixr22uu`
- Public Key: `64lPicoowXXVfWt3y`

## Dev

```bash
npm install
npm run dev      # local dev server
npm run build    # production build
```
