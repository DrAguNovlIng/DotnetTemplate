# DotnetTemplate

Shared baseline for .NET Aspire solutions: **AppHost** and **ServiceDefaults**. Add API, data, UI, and test projects when a repo needs them.

Target framework: **net10.0** (see [Directory.Build.props](Directory.Build.props)).

## Central packages

[Directory.Packages.props](Directory.Packages.props) pins versions for stacks used across repos: Aspire hosting (including SQL Server), EF Core, OpenTelemetry, xUnit v3, Shouldly, Testcontainers (including MsSql), and `Microsoft.AspNetCore.Mvc.Testing` for integration smoke tests.

## Conventions for code added in consuming repos

- **HTTP APIs:** MVC controllers with `[Route("api/[controller]")]`. Controllers stay thin: parse input, auth, map `Result` to HTTP. Domain logic and EF stay in **Store** classes.
- **Database:** EF Core in Store classes. Register `I*Store` + implementation as scoped. Do not inject `DbContext` into controllers. Prefer batch/list overloads instead of calling Store methods in loops.
- **Errors (net10.0):** Use local `Result` / `Result<T>` types for expected failures; exceptions are for truly unexpected failures. Do not add Result NuGet packages.
- **DTOs:** Sealed records; API DTOs colocate with the controller with a static mapper class.
- **Tests:** xUnit v3 with `TestContext.Current.CancellationToken` on async calls; Shouldly for assertions; Testcontainers (MsSql) for Store tests with real SQL; `WebApplicationFactory` for HTTP smoke tests in a separate integration project. Do not mock Stores or use EF InMemory/SQLite as a substitute for Store tests.
- **Migrations:** Generate and review EF Core migrations with tooling; do not hand-write migration files.

## Avoid unless a repo already uses them

FluentAssertions, FluentResults/ErrorOr, AutoMapper, MediatR/CQRS handlers, FluentValidation, repository/unit-of-work abstractions, Minimal APIs for HTTP, generated API clients (NSwag, Refit), and bUnit/MudBlazor when the repo has no Blazor UI stack.

Before adding a **new** NuGet package, check license metadata (MIT, Apache-2.0, and BSD-3-Clause are fine without asking).
