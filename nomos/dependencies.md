# Nomos - Dependencies

Nomos treats package and build management as an engineering discipline, not an afterthought.

## Central Package Management

Nomos manages NuGet packages centrally, treating package versions as a single source of truth.
He does this through a `Directory.Packages.props` file at the repository root.

### How it works

The file enables Central Package Management with `<ManagePackageVersionsCentrally>true</ManagePackageVersionsCentrally>`.
Every direct package is declared as a `<PackageVersion Include="..." Version="..." />` entry.
Individual `.csproj` files then declare only `<PackageReference Include="..." />` with no `Version` attribute.

### Single source of truth

When a package version changes, the change happens in exactly one place.
This makes version drift impossible by construction and keeps the dependency graph auditable at a glance.
Nomos keeps the list alphabetical and free of orphaned versions that no project references.

### What he pins

The central file carries the transport, messaging, logging, and test-stack packages, with every version in one alphabetical list.
It also pins `JetBrains.Annotations`, which feeds the inspection workflow.

### Rules

Nomos never hardcodes a package version inside an individual `.csproj` file.
He never adds a package to a project that a more appropriate project already owns.
He prefers the platform's own packages over third-party alternatives when the capability is equivalent.

## Package locking

Nomos locks the dependency graph so that every restore is deterministic and reproducible.
He enables NuGet lock files through the same `Directory.Packages.props` that drives Central Package Management.

### Enabling lock files

`<RestorePackagesWithLockFile>true</RestorePackagesWithLockFile>` tells restore to produce a `packages.lock.json` for every project.
These lock files are committed to source control alongside the code.
Nomos treats them as first-class build artifacts, not generated noise to be ignored.

### Locked restore mode

`<RestoreLockedMode>true</RestoreLockedMode>` enforces locked, CI-style restore.
In this mode the build fails if the lock file does not match the dependency graph, instead of silently drifting.
Nomos wants a missing or stale lock file to be a loud, deliberate event.

### What the lock file pins

Each `packages.lock.json` records the exact resolved version and `contentHash` for every dependency.
It distinguishes direct from transitive packages, so the full graph is visible and auditable.
This is what guarantees that a build today resolves to the same bytes as a build next month.

### Regenerating deliberately

Nomos regenerates lock files with `dotnet restore --force-evaluate` when the dependency graph changes.
He never lets an implicit restore drift the lock file without his awareness.
The lock file changes together with the `Directory.Packages.props` update in the same change set.

## Dependency security

Nomos runs an audit tool over his NuGet dependencies as part of his normal workflow.
The tool flags versions that carry known CVEs and releases that are too recent to trust.
To avoid poisoned releases, he prefers staying on older, vetted packages rather than the cutting edge.
When the audit tool's policy clashes with his usual zero-warning stance, he compromises and accepts those warnings.
He treats the policy enforced by the audit tool as the higher authority over being current.
