# Development Roadmap

Every milestone must include source, commands, observed results, and limitations before it is marked complete.

## Milestone 0 — Environment record

- Record distribution, kernel release, architecture, compiler, and kernel-header package/source.
- Decide between a disposable VM for software-only work and a separate board for hardware work.
- Add exact prerequisites and cleanup instructions.

**Acceptance:** another person can reproduce the build environment from the documentation.

## Milestone 1 — Minimal module lifecycle

- Implement initialization and exit paths.
- Build as an out-of-tree module with warnings enabled.
- Load, inspect logs, unload, and repeat.
- Test a deliberate initialization failure and verify cleanup.

**Acceptance:** repeated load/unload cycles leave no registered resource behind.

## Milestone 2 — Basic device interface

- Register one character or miscellaneous device.
- Add `open`/`release` accounting.
- Implement bounded `read` and `write` behavior.
- Return correct errors for invalid lengths and unsupported operations.

**Acceptance:** documented shell and C-client tests exercise normal and boundary cases.

## Milestone 3 — Shared UAPI and ioctl

- Define a minimal versioned shared header.
- Add one read-only and one state-changing command.
- Validate command type, number, direction, size, and user buffers.
- Test unknown commands and invalid parameters.

**Acceptance:** C and Python clients produce the same verified results for supported commands.

## Milestone 4 — Blocking I/O and poll

- Add a wait queue and explicit readiness condition.
- Support blocking/non-blocking operation as designed.
- Implement `poll` readiness masks.
- Test timeout, interruption by a signal, and wake-up behavior.

**Acceptance:** event-driven tests do not rely on busy waiting and have deterministic timeouts.

## Milestone 5 — Concurrency and robustness

- Protect shared state with context-appropriate synchronization.
- Run multiple readers/writers.
- Test unload refusal or safe teardown while resources are active.
- Review all allocation, copy, and error-unwind paths.

**Acceptance:** repeatable stress tests show no warnings, leaks, hangs, or corrupted state in the tested environment.

## Milestone 6 — Python automation and reports

- Build fixtures for device discovery, setup, cleanup, and log capture.
- Add positive, negative, boundary, concurrency, and interruption tests.
- Produce a timestamped summary containing environment metadata.
- Keep expected failures separate from unexpected failures.

**Acceptance:** one documented command runs the suite and produces an auditable report.

## Milestone 7 — Fault injection

- Exercise invalid arguments, partial operations, allocation failures where safely supported, timeout, and interrupted waits.
- Verify error codes and cleanup after each injected failure.
- Document unsupported or unsafe injection cases.

**Acceptance:** each failure has a repeatable trigger and verified recovery behavior.

## Milestone 8 — GPIO interrupt integration

- Select the exact board, SoC, GPIO, kernel, and device-tree binding.
- Verify safe wiring and voltage levels.
- Request/configure the interrupt through supported kernel interfaces.
- Keep the hard interrupt handler minimal and defer work when necessary.
- Expose events through the already-tested wait/poll interface.

**Acceptance:** captured hardware evidence links physical events to driver wake-ups without inventing pin or IRQ details.
