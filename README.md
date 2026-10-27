# Full Stack Open CI/CD

This repository is used for the CI/CD module [part 11](https://courses.mooc.fi/org/uh-cs/courses/full-stack-open-continuous-integration) of the Full Stack Open course.

The app is a Pokedex built with React, React Router, and webpack. It lists the first 50 Pokémon from [PokéAPI](https://pokeapi.co/), and each Pokémon has its own page with its abilities and stats. In production, a small Express server (`app.js`) serves the built files and a `/health` endpoint.

## Live Project Link

View the [Pokedex](https://fso-pokedex-iod5.onrender.com/) project.

## Commands

Start by running `npm install` inside the project folder. The pipeline uses Node.js 24.

- `npm start` to run the webpack dev server at <http://localhost:8080>
- `npm test` to run the unit tests (Jest and React Testing Library, in `test/`)
- `npm run test:e2e` to run the end-to-end tests (Playwright, in `e2e-tests/`)
- `npm run eslint` to run ESLint
- `npm run build` to make a production build in `dist/`
- `npm run start-prod` to serve the production build at <http://localhost:5001> (or the port set in `PORT`)

### End-to-End Tests

The end-to-end tests run against the production build, and Playwright starts the server for you. Build the app and install the browser first:

```bash
npm run build
npx playwright install chromium
npm run test:e2e
```

The tests load real data from PokéAPI, so they need an internet connection.

## Pipeline

The pipeline is defined in `.github/workflows/pipeline.yml`.

- **Pull requests to `main`:** three jobs run in parallel: linting, unit tests, and end-to-end tests (which build the app first). Nothing is deployed.
- **Pushes to `main`** (merged pull requests): once the checks pass, the app is deployed to Render through a deploy hook, and a patch version tag is created.
- **`#skip`:** if any commit in a push has `#skip` in its message, the deploy and the tagging are skipped.
- **Branch protection:** `main` is protected by a ruleset, so the required checks must pass before a pull request can be merged.
- **Notifications:** Discord messages report failed builds (with commit and run links) and successful deployments.
- **Health check:** `.github/workflows/health_check.yml` checks the live app's `/health` endpoint every day at 06:00 UTC, retrying up to six times. It can also be run by hand from the **Actions** tab.
- **Hello World:** `.github/workflows/hello.yml` is the first workflow from the course exercises. It prints a greeting, the date, and the files on every push to `main`.

The workflows use two repository secrets: `RENDER_DEPLOY_HOOK` and `DISCORD_WEBHOOK`.

## Known Limitations

- **Pokémon page links only work from inside the app.** The production server only serves the files in `dist/`, so opening or refreshing a URL like `/pokemon/ivysaur` directly returns a 404. The dev server does not have this problem.

## Exercises 21 and 22 (own pipeline)

Exercises 21 and 22 are in a separate repository, which builds a similar CI/CD pipeline for my own phonebook application.

- [Phonebook Repository](https://github.com/sjdumas/fso-phonebook)
- [Phonebook Live App](https://fso-phonebook-ci-cd.onrender.com/)
