# Nomos - Architecture

Nomos builds every service as a strict onion, also known as clean or hexagonal, architecture.
Dependencies always point inward toward the domain, and never the other way around.

## Layers

### Domain

The Domain is the center of the onion and has zero project dependencies.
It contains the entities, value objects, domain services, and the ports that outer layers implement.
Domain code never references web frameworks, messaging libraries, ORMs, or any other infrastructure or framework package beyond logging abstractions.
The Domain owns the vocabulary of the system and the invariants that make the system correct.

### Application

The Application layer orchestrates use cases against the Domain.
It depends only on the Domain and defines the application-facing ports it consumes.
Application code coordinates domain objects and ports, but never contains business rules that belong in the Domain.

### Infrastructure

The Infrastructure layer adapts the Domain and Application to the outside world.
It implements the ports defined by the inner layers: persistence, messaging, RPC transports, and serialization.
Nomos splits infrastructure into focused projects by concern rather than one sprawling adapter project.

### Host

The Host is the composition root and the entry point of the process.
It wires dependency injection, maps configuration, and exposes the HTTP and RPC surfaces.
The Host knows about everything, but nothing knows about the Host.
Although it plays the Host role, the project is not named `Host`: Nomos names it with the bare root name, such as `FooBar`, while the inner projects carry that root as a prefix, such as `FooBar.Domain`, `FooBar.Application`, and `FooBar.Infrastructure.*`.

## Ports

Ports live in the layer that consumes them, not the layer that implements them.
The Domain and Application define the interfaces, and the Infrastructure projects provide the concrete adapters.

## Solution layout

Application projects live strictly inside `/src`.
Nomos keeps test suites in a `tests` folder, but he will use an existing `test` folder if one is already there.
The solution file lives at the repository root and is the only composition point for the full build.
Nomos prefers the XML-based `.slnx` solution format over the legacy `.sln` format.
Solution folders mirror the top-level directories so the solution view matches the filesystem.
Each project has a single, explicit responsibility and a name that matches its layer and purpose.

## Rules Nomos will not bend on

No project may reference a project from an inner layer in the wrong direction.
No `using` of an infrastructure namespace may appear in the Domain or Application projects.
Test projects may reference the projects under test, but must never be referenced by application projects.
New projects are only added when an existing project cannot own the responsibility without violating a boundary.
