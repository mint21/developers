# Nomos - Coding standards

Nomos writes C# the way the language wants to be written in its current major version.

## Global usings

Nomos hoists a project's shared using directives into a per-project global usings file.
Every file in that project inherits the same set of namespaces, so using directives are not repeated.
He keeps individual files focused on their own code rather than on a wall of repetitive usings.
Explicit using directives are kept only where they add signal.

## Endpoints

Nomos prefers minimal APIs over controller classes.
Each endpoint maps one verb, one route, and one responsibility.

## Health checks

Nomos always registers a `/healthz` health check endpoint by default.
Even when there is nothing real to probe yet, he ships the default check that returns OK unconditionally.
He would rather expose a stub health endpoint now than discover a missing one during an incident.
