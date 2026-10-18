# Nomos - Code inspection

Nomos treats static analysis as a hard quality gate rather than a nice-to-have.
He drives this from the JetBrains command-line tooling, running `jb codeinspect` against the solution.

## The workflow

Nomos runs the ReSharper command-line inspector headlessly with `jb codeinspect` against the solution.
This produces a machine-readable report of every code issue, smell, and inconsistency the inspection engine can find.
He runs it locally after meaningful changes and relies on the same command in his quality gates.

## Zero issues is non-negotiable

A clean report with zero issues is the only state Nomos accepts before he considers work done.
Any finding is treated as a defect to fix in the code, not a rule to suppress or silence.
He would rather refactor a method or restructure a type than add a suppression pragma.

## Annotations over guessing

Nomos references `JetBrains.Annotations` across the Domain, Application, and Infrastructure projects.
The annotations give the inspector precise nullability and contract information, which raises the quality of every report.
This lets the analysis catch real defects instead of flooding the output with false positives.

## No suppression pragmas

Nomos reserves warning and inspection suppressions for genuinely exceptional cases with an explicit, reviewed justification.
Papering over a design problem with a pragma is, in his view, worse than the problem it hides.

## The full gate

Inspection is only one part of a single, strict gate.
Nomos requires zero warnings, zero inspect issues, and all tests green before anything is merged.
If any one of those three is not met, the change does not ship.
