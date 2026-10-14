# Executable BDD Tooling

Read when adopting/configuring a BDD runner or generating/running tests. Use [SKILL.md](SKILL.md#drive-tests-from-scenarios) for the deterministic contract and [REFERENCE.md](REFERENCE.md#deterministic-contract-and-adoption) for adoption/ownership boundaries.

## Choose And Integrate

Inspect the project's test commands, dependencies/lockfile, runtime, module format, and existing feature/binding/config paths. Choose an installed compatible runner before adding one. For authorized new executable BDD, add only the missing setup; preserve existing tests and integrate into the project's runner/CI command. Persist runner-level feature/support paths and loader settings in versioned runner-appropriate config or a project script, and expose any required generation plus execution as one reproducible project command invoked by the local workflow and existing CI; direct runners need no artificial generation. Discussion, editorial work, and legacy work outside scoped adoption need no installation.

Use the project's package manager, environment, dependency constraints, and lockfile. The commands below show package names with npm or pip as examples, not a request to switch managers or upgrade existing dependencies. Select compatible versions before installation and consult their matching official docs; `main`, `stable`, and `latest` documentation can differ from installed releases. Run local project binaries in the existing workspace/virtual environment, not global tools or commands that fetch a different runner on demand.

| Project fit | Tool/packages to add if missing | Feature-to-test execution | Required connection |
| --- | --- | --- | --- |
| JS/TS with Playwright Test | `playwright-bdd`, plus `@playwright/test` if absent | `bddgen` generates runnable wrappers; Playwright runs them | `defineBddConfig` feature/step paths and its returned `testDir`; reviewed steps/fixtures |
| JS/TS with direct Gherkin execution | `@cucumber/cucumber`; compatible TS transpiler if needed | `cucumber-js` reads features directly | Feature paths, loaded support code, correct JS/TS module loader |
| Python using pytest | `pytest-bdd` | pytest collects scenario bindings and executes feature steps | Discoverable `test_*.py` module with `scenarios()` or `@scenario`, visible step definitions/fixtures |
| Python with a direct feature runner | `behave` | `behave` reads features directly | Feature directory containing `steps/` and Python step implementations |
| .NET test project | One matching `Reqnroll.NUnit`, `Reqnroll.MSTest`, or `Reqnroll.xUnit` package | MSBuild generates test classes; existing test framework runs them | Existing test SDK/adapter, included features and compiled `[Binding]` classes |

The matrix is non-exhaustive: for unlisted languages/test frameworks, use a compatible feature runner following its version-specific primary documentation and verify the real feature/binding connection and execution of every required case; if no viable runner path exists, report an integration gap rather than treating passing ordinary tests as deterministic acceptance.

Bind every step to real setup/action/assertions and inspect required outcomes, including state and prohibited side effects. Tool installation, discovery, generated empty methods, or a dry run alone is not acceptance. Prefer service/API/domain fixtures when they prove the behavior; browser tooling is needed only for browser cases.

## Playwright-BDD

For an existing compatible Playwright project, install only the adapter:

```sh
npm install --save-dev playwright-bdd
```

If Playwright Test is also missing:

```sh
npm install --save-dev @playwright/test playwright-bdd
```

Check Node/Playwright compatibility for the selected adapter version. Merge into the existing config; retain its fixtures, projects, filters, and setup. If ordinary Playwright tests already exist, retain explicit non-BDD project(s) with their own `testDir` and prior settings, and use the BDD `testDir` only in BDD projects. If the old config has only a top-level `testDir`, add a non-BDD project preserving that directory and inherited settings when introducing `projects`; do not replace ordinary test discovery with BDD-only projects. Keep non-BDD selection excluding generated BDD output through a separate directory or `testIgnore`, so ordinary tests remain discovered without running BDD wrappers twice. When no config exists, this minimal `playwright.config.ts` discovers TS steps and features:

```ts
import { defineConfig } from '@playwright/test';
import { defineBddConfig } from 'playwright-bdd';

export default defineConfig({
  testDir: defineBddConfig({
    features: 'features/**/*.feature',
    steps: 'features/steps/**/*.ts',
  }),
});
```

For JS steps, change the step glob to the existing `.js` paths/module format. In loaded step source, import `createBdd` from `playwright-bdd`, obtain `Given`, `When`, `Then` with `createBdd()`, and register matching implementations. For custom fixtures, extend/export the `test` base from `playwright-bdd`, reuse the project's fixture implementations, and bind steps with `createBdd(test)`. Implement assertions with Playwright `expect` or the project's assertion library.

Only when steps use browsers, install the browser(s) selected by the project's Playwright configuration, for example Chromium:

```sh
npx playwright install chromium
```

Generate from current features/bindings/config and execute, preserving failure short-circuiting:

```sh
npx bddgen && npx playwright test
```

Use the same selected config for both commands if it is not the default. With pnpm, use `pnpm exec bddgen && pnpm exec playwright test`. For Yarn PnP, first consult the selected adapter version's [official installation PnP setup](https://github.com/vitalets/playwright-bdd/blob/main/docs/getting-started/installation.md#yarn) and apply its required `packageExtensions` in `.yarnrc.yml` using compatible dependency versions; then use `yarn bddgen && yarn playwright test`. Keep wrappers (default `.features-gen/`) read-only; fix source and regenerate. Do not run stale wrappers after generation fails. Inspect scenario/Examples identities in results against the required scope.

Official docs: [installation](https://github.com/vitalets/playwright-bdd/blob/main/docs/getting-started/installation.md), [configuration and first run](https://github.com/vitalets/playwright-bdd/blob/main/docs/getting-started/write-first-test.md), [custom fixtures](https://github.com/vitalets/playwright-bdd/blob/main/docs/writing-steps/playwright-style.md#custom-fixtures), [multiple projects](https://github.com/vitalets/playwright-bdd/blob/main/docs/configuration/multiple-projects.md), [browser installation](https://playwright.dev/docs/browsers#install-browsers).

## Cucumber-JS

Install locally in the existing JS/TS package:

```sh
npm install --save-dev @cucumber/cucumber
```

Implement `Given`, `When`, `Then` from `@cucumber/cucumber` in loaded support files, with scenario state in the project's World/hooks and real assertions (for example `node:assert/strict`). Cucumber executes features directly; no wrapper-generation command is required. Reuse the existing config/profile and include hooks/World files in its support paths. For a minimal setup without configured paths, these setup examples explicitly load the steps; persist their path/loader arguments in the reusable config or project command:

```sh
npx cucumber-js 'features/**/*.feature' --require 'features/step-definitions/**/*.js'
```

That command is for CommonJS JS. For ESM JS, use `--import` instead of `--require`. TS needs a compatible loader; reuse an existing one or, for the current documented `tsx` approach, install it:

```sh
npm install --save-dev tsx
npx cucumber-js 'features/**/*.feature' --require-module tsx/cjs --require 'features/step-definitions/**/*.ts'
```

That TS command is for CommonJS. For ESM TS, add an ESM setup file `tsx-register.js` (in a package with `"type": "module"`):

```js
import { register } from 'tsx/esm/api';
register();
```

Load it before the TS support files:

```sh
npx cucumber-js 'features/**/*.feature' --import ./tsx-register.js --import 'features/step-definitions/**/*.ts'
```

Choose only the matching loader/config; do not add a second transpiler or competing config. Undefined/pending snippets are editable authoring scaffolds needing implementations and assertions, not passed tests.

Official docs: [installation](https://github.com/cucumber/cucumber-js/blob/main/docs/installation.md), [discovery/configuration](https://github.com/cucumber/cucumber-js/blob/main/docs/configuration.md), [TS/module loading](https://github.com/cucumber/cucumber-js/blob/main/docs/transpiling.md). Check the release's docs rather than assuming `main` applies.

## Pytest-BDD

Install in the project's existing Python test environment (pip example):

```sh
python -m pip install pytest-bdd
```

Features alone are not collected as pytest tests. Reuse existing `scenarios()`/`@scenario` bindings; add a new test module only for features/scenarios not already bound, without binding the same scenario twice. If the entire feature directory is unbound, this minimal `tests/test_acceptance.py` binds it explicitly; paths default to being relative to that test module:

```python
from pytest_bdd import scenarios

scenarios("../features")
```

Use `@scenario` instead when individual binding is needed, selecting only unbound scenarios. Define matching `@given`, `@when`, `@then` steps from `pytest_bdd` in the collected module; put shared steps in a visible parent `conftest.py`, since another test module's steps are not automatically visible. Reuse pytest fixtures for state/action and put actual assertions in the steps. Retain existing `bdd_features_base_dir` if configured and adapt paths accordingly. Ensure pytest's configured discovery includes the binding modules (`test_*.py` by default), then run the existing modules or, for the example above:

```sh
python -m pytest tests/test_acceptance.py
```

Optional authoring help, only from an existing feature whose scenarios are all unbound, to a **new** test module in an existing test directory (the example assumes `cancellation.feature` is unbound):

```sh
pytest-bdd generate features/cancellation.feature > tests/test_cancellation.py
```

This prints empty scenario/step source skeletons. Review and edit them into maintained source with real implementations/assertions; do not overwrite existing bindings, create a second feature copy/binding, or regenerate over completed source. Reuse shared steps from `conftest.py`. Adjust generated feature paths for the module location/base directory and ensure pytest collects it. These skeletons are authoring scaffolds, **not runner-derived output** subject to the read-only rule. Unlike `bddgen` wrappers, they are not acceptance-ready just because generation succeeds. Runtime pytest-bdd still reads the feature through scenario bindings.

Official docs: [installation and scenario binding](https://pytest-bdd.readthedocs.io/en/stable/), [feature paths](https://pytest-bdd.readthedocs.io/en/stable/#feature-file-paths), [editable code generation helpers](https://pytest-bdd.readthedocs.io/en/stable/#test-code-generation-helpers), [pytest discovery](https://docs.pytest.org/en/stable/explanation/goodpractices.html#conventions-for-python-test-discovery).

## Behave

Install in the existing Python environment (pip example):

```sh
python -m pip install behave
```

Use the existing feature root. Minimal layout:

```text
features/
  cancellation.feature
  steps/
    cancellation.py
```

Register matching `given`, `when`, `then` decorators from `behave` in step files; implementations take `context`, perform setup/action, and assert observable results. Reuse `features/environment.py` hooks when needed. Run directly, without generated wrappers:

```sh
behave features
```

Undefined-step snippets need implementation/assertions; copying them is not acceptance.

Official docs: [installation](https://behave.readthedocs.io/en/stable/install/), [layout, steps, and execution](https://behave.readthedocs.io/en/stable/tutorial/).

## Reqnroll

Inspect the target `.csproj`, SDK/framework versions, central package management, and existing test SDK/adapter first. Add **one** Reqnroll integration matching that project's test framework; retain its compatible test SDK/adapter. For an existing MSTest project, from its project directory:

```sh
dotnet add package Reqnroll.MSTest
```

For NUnit or xUnit, substitute `Reqnroll.NUnit` or `Reqnroll.xUnit`; do not install all three. Honor centrally managed versions rather than adding conflicting project versions. Include `.feature` files in that test project and compile `[Binding]` classes with matching `[Given]`, `[When]`, `[Then]` methods, setup/action, and assertions from the existing test framework/library. Linked external features and output-location settings are version-dependent; use the installed version's documented build configuration.

These integration packages bring `Reqnroll.Tools.MsBuild.Generation`; MSBuild generates and compiles feature test classes. In the test project directory:

```sh
dotnet test
```

By default this restores/builds (including generation) before execution. Use the project's configured test command if it wraps this flow; avoid `--no-build` unless the build is verified current for the same inputs. Treat generated `*.feature.cs` as read-only output; edit feature/binding source and rebuild. No separate AI conversion or standalone generator script is needed.

Official docs: [existing project setup](https://docs.reqnroll.net/latest/installation/setup-project.html#setup-an-existing-test-project), [MSBuild generation](https://docs.reqnroll.net/latest/installation/configuring-build.html), [execution](https://docs.reqnroll.net/latest/execution/executing-reqnroll-scenarios.html).

## Check The Connection

At the planned checkpoint, run the project's integrated BDD command, reconcile required scenario/Examples identities with execution and binding assertions, and record actual results/gaps in existing task evidence under [SKILL.md](SKILL.md#verify-and-hand-off). Generation/discovery success is setup evidence; only executed assertions establish behavior evidence. Do not claim required excluded/skipped/pending/undefined cases passed or substitute unrelated green tests.
