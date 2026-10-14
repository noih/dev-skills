# Executable BDD Tooling

Read when adopting/configuring a BDD runner or generating/running tests. Use [SKILL.md](SKILL.md#drive-tests-from-scenarios) for the deterministic contract and [REFERENCE.md](REFERENCE.md#deterministic-contract-and-adoption) for adoption/ownership boundaries.

## Choose And Integrate

Inspect the project's test commands, dependencies/lockfile, runtime, module format, and existing feature/binding/config paths. Choose an installed compatible runner before adding one. For authorized new executable BDD, add only the missing setup; preserve existing tests and integrate into the project's runner/CI command. Persist runner-level feature/support paths and loader settings in versioned runner-appropriate config or a project script, and expose any required generation plus execution as one reproducible project command invoked by the local workflow and existing CI; direct runners need no artificial generation. Discussion, editorial work, and legacy work outside scoped adoption need no installation.

For new authorized adoption without an existing compatible runner, default to Cucumber.js for general JS/TS Node/API testing, or `playwright-bdd` when Playwright Test already exists or browser-first testing calls for Playwright; use Cucumber-JVM on JUnit Platform for Java, the `cucumber` crate for Rust, and `godog` for Go. These defaults do not require migrating a working runner.

Use the project's package manager, environment, dependency constraints, and lockfile. The commands below show package names with npm or pip as examples, not a request to switch managers or upgrade existing dependencies. Select compatible versions before installation and consult their matching official docs; `main`, `stable`, and `latest` documentation can differ from installed releases. Run local project binaries in the existing workspace/virtual environment, not global tools or commands that fetch a different runner on demand.

| Project fit | Tool/packages to add if missing | Feature-to-test execution | Required connection |
| --- | --- | --- | --- |
| JS/TS with Playwright Test or browser-first testing | `playwright-bdd`, plus `@playwright/test` if absent | `bddgen` generates runnable wrappers; Playwright runs them | `defineBddConfig` feature/step paths and its returned `testDir`; reviewed steps/fixtures |
| General JS/TS Node/API testing | `@cucumber/cucumber`; compatible TS transpiler if needed | `cucumber-js` reads features directly | Feature paths, loaded support code, correct JS/TS module loader |
| Java on JUnit Platform | `io.cucumber:cucumber-java`, `io.cucumber:cucumber-junit-platform-engine`, `org.junit.platform:junit-platform-suite` | JUnit Platform discovers and executes features through the suite | Test dependencies, discoverable `@Suite`, feature resources and glue packages |
| Rust | `cucumber` and a compatible async executor in dev-dependencies | A Cargo test target invokes the feature runner | `[[test]]` with `harness = false`, a `World`, registered steps, awaited runner |
| Go | `github.com/cucumber/godog` in the project module | A Go test entrypoint invokes the feature runner | Discoverable `TestFeatures`, scenario initializer, feature paths, strict status propagation |
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

After saving paths/support/loader settings in the existing Cucumber config/profile, add a package script such as `"test:bdd": "cucumber-js"` and invoke `npm run test:bdd` (or the project's package-manager equivalent) from the existing test/CI command, preserving ordinary tests and failure propagation. Keep strict pending-step handling enabled; required skipped or missing cases still need the final execution reconciliation.

Official docs: [installation](https://github.com/cucumber/cucumber-js/blob/main/docs/installation.md), [discovery/configuration](https://github.com/cucumber/cucumber-js/blob/main/docs/configuration.md), [TS/module loading](https://github.com/cucumber/cucumber-js/blob/main/docs/transpiling.md). Check the release's docs rather than assuming `main` applies.

## Java: Cucumber-JVM On JUnit Platform

Add `io.cucumber:cucumber-java` and `io.cucumber:cucumber-junit-platform-engine` as Maven test-scope dependencies or Gradle `testImplementation` dependencies. Include `org.junit.platform:junit-platform-suite` (suite API and engine), or its matching API/engine artifacts in their appropriate test compile/runtime configurations. Preserve compatible Cucumber/JUnit dependency management and build-plugin versions; upstream `main` currently describes JUnit 6, so select documentation matching the project's JDK/JUnit/Cucumber versions.

Put features under `src/test/resources/com/example/bdd/` and glue under `src/test/java/com/example/bdd/`, adapting both to existing paths. Register real `@Given`, `@When`, `@Then` methods from `io.cucumber.java.en` in that glue package, reuse scenario state/hooks, and assert with the project's test assertion library. This `src/test/java/com/example/bdd/RunCucumberTest.java` selects the matching feature resource package:

```java
package com.example.bdd;

import org.junit.platform.suite.api.ConfigurationParameter;
import org.junit.platform.suite.api.IncludeEngines;
import org.junit.platform.suite.api.SelectPackages;
import org.junit.platform.suite.api.Suite;

@Suite
@IncludeEngines("cucumber")
@SelectPackages("com.example.bdd")
@ConfigurationParameter(key = "cucumber.glue", value = "com.example.bdd")
public class RunCucumberTest {}
```

Ensure Maven Surefire's JUnit Platform provider discovers this `*Test` suite, or configure the existing Gradle test task with `useJUnitPlatform()` and include the suite in discovery. Retain ordinary test engines and filters outside this Cucumber-only suite; do not also select the same features through competing root discovery or `cucumber.features`. Run `./mvnw test` or `./gradlew test` from the module using its existing wrapper (otherwise its configured Maven/Gradle command), and keep that flow in the existing local/CI command. Undefined/pending steps and assertion failures must fail execution; excluded, skipped, or undiscovered required cases remain gaps even when ordinary tests pass.

Official docs: [Cucumber JUnit Platform engine and build discovery](https://github.com/cucumber/cucumber-jvm/tree/main/cucumber-junit-platform-engine), [Java step definitions](https://github.com/cucumber/cucumber-jvm/tree/main/cucumber-java), [JUnit suite API/engine](https://docs.junit.org/current/advanced-topics/junit-platform-suite-engine.html), [Gradle test configuration](https://docs.gradle.org/current/userguide/java_testing.html).

## Rust: Cucumber

Add a compatible `cucumber` version with its `macros` feature enabled to `[dev-dependencies]` in `Cargo.toml`; reuse the project's async executor, or add compatible `futures` with executor support for the entrypoint below. Keep versions/lockfile under the project's existing dependency policy. Add this target alongside existing tests:

```toml
[[test]]
name = "bdd"
harness = false
```

In `tests/bdd.rs`, define a scenario state `AcceptanceWorld` deriving `Debug`, `Default`, and `cucumber::World` (or use the documented custom initializer). Compile its step modules into this target; register matching `#[given]`, `#[when]`, `#[then]` attributes from `cucumber`, with real setup/actions and assertions or failing `Result` returns. Once that World and its steps exist, this entrypoint awaits execution:

```rust
use cucumber::World;

fn main() {
    futures::executor::block_on(async {
        AcceptanceWorld::cucumber()
            .fail_on_skipped()
            .run_and_exit("tests/features")
            .await;
    });
}
```

`fail_on_skipped()` makes unmatched/skipped steps fail, except scenarios tagged `@allow.skipped`; do not use that exemption for required cases. `run_and_exit()` propagates parsing/step failures through a panic and nonzero test result; `World::run()` alone does not enforce skipped-step failure. Use `cargo test --test bdd` from the crate root for this target, retaining it in the existing project test/CI command alongside ordinary tests; `cargo test` does not independently scan `.feature` files. Missing or filtered required scenarios still need final reconciliation even with zero exit status.

Official docs: [quickstart and Cargo target/executor setup](https://cucumber-rs.github.io/cucumber/current/quickstart.html), [World and automatic step registration](https://docs.rs/cucumber/latest/cucumber/trait.World.html), [skipped-step handling and run result propagation](https://docs.rs/cucumber/latest/cucumber/struct.Cucumber.html#method.fail_on_skipped). Match these APIs to the selected crate version rather than copying the quickstart's example versions.

## Go: Godog

Add a compatible `github.com/cucumber/godog` version to the existing module's `go.mod`/`go.sum` through the project's dependency workflow; no global Godog CLI is needed. In a discoverable `*_test.go` file, implement `InitializeScenario(sc *godog.ScenarioContext)` and register matching expressions with `sc.Step(expression, implementation)`, resetting state per scenario through context/hooks. Steps must perform real actions and return assertion errors or fail via the test framework, not return `nil` unconditionally.

With that initializer implemented in the same test package, use this entrypoint (adapt the package name and feature path; paths are relative to the tested package):

```go
package acceptance_test

import (
    "testing"

    "github.com/cucumber/godog"
)

func TestFeatures(t *testing.T) {
    suite := godog.TestSuite{
        ScenarioInitializer: InitializeScenario,
        Options: &godog.Options{
            Format:   "pretty",
            Paths:    []string{"features"},
            TestingT: t,
            Strict:   true,
        },
    }
    if status := suite.Run(); status != 0 {
        t.Fatalf("Godog failed with status %d", status)
    }
}
```

`TestingT` exposes scenario subtests; `Strict: true` makes pending/undefined/ambiguous steps fail, and the explicit status check propagates suite failure. Run `go test -count=1 -v ./...` from the module root through the existing local/CI command to execute features afresh and retain regular Go tests. Do not replace an existing `TestMain`; ensure its filters/build tags include this entrypoint, and reconcile required scenario identities rather than treating an empty or filtered green run as acceptance.

Official docs: [module usage and Go Test function integration](https://github.com/cucumber/godog#subtests-of-testingt), [strict and test options](https://github.com/cucumber/godog/blob/main/internal/flags/options.go). Use the selected module version's APIs.

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
