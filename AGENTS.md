# AGENTS.md: IT Joke of the Day

A single static web page that shows a random IT joke. A **Next** button shows another random joke, never the one already on screen. The page has no server, no database, no accounts and no tracking.

## Stack
- Vite + TypeScript (strict). Plain DOM, no UI framework.
- pnpm for packages. Commit `pnpm-lock.yaml` with every dependency change.
- Vitest with the jsdom environment for tests, plus `vitest-axe` for accessibility checks.
- ESLint (typescript-eslint), Prettier, and html-validate (its `html-validate:recommended` and a11y rules).

## Layout
- `index.html`: the page.
- `src/main.ts`: wires the page (shows the first joke, handles Next).
- `src/jokes.ts`: picks a random joke, for example `pickJoke(jokes: readonly string[], exclude?: string, random?: () => number): string`. Keep it pure so tests can pass a fixed `random`.
- `src/jokes-data.ts`: the joke list, exported as `readonly string[]`. Jokes are short, workplace-safe, English and unique.
- `src/style.css`: styles.
- `tests/*.test.ts`: all tests.

## Commands (the gate runs these)
- `pnpm exec prettier --check .`
- `pnpm exec eslint .`
- `pnpm exec html-validate index.html`
- `pnpm exec tsc --noEmit`
- `pnpm exec vitest run`
- `pnpm exec vite build`

All of them must pass before a change is done. Tests run offline in jsdom: no network, no real browser, and no localhost servers in tests.

## How to work
- Test-first: each change starts with a failing test in `tests/` that names the behaviour, then the code that makes it pass.
- Keep changes small and inside the ticket's scope.
- Make randomness injectable so tests are deterministic. Never assert on real `Math.random` output.

## Security (OWASP, as it applies to a static page)
- Put jokes on the page with `textContent`, never `innerHTML`, `outerHTML`, `insertAdjacentHTML`, `document.write` or `eval`.
- No third-party scripts, fonts, CDNs or analytics. Everything is bundled from the repo.
- `index.html` keeps a restrictive Content Security Policy meta tag (at least `default-src 'self'`, no `unsafe-inline` or `unsafe-eval` for scripts).
- No secrets, tokens or keys in the code or config.
- Add a dependency only when it's needed; prefer dev dependencies.

## Accessibility (WCAG 2.2 AA)
- Use a real `<button type="button">` with a visible text label ("Next"), not a clickable div.
- The joke sits in a region screen readers announce when it changes (for example `aria-live="polite"`).
- The page has `lang="en"`, a `<title>`, one `<h1>` and a `<main>` landmark.
- Keep a visible focus indicator, use colour contrast of at least 4.5:1, and never use colour alone to convey meaning.
- Everything works with the keyboard alone.
- Every test that renders the page also runs an axe check (`vitest-axe`) and expects no violations.

## Personal data
- The page collects no personal data. No cookies, no `localStorage` of user data, no logging of user actions.
