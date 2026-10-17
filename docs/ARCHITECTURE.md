# Planned Architecture

## Design boundary

The project separates the kernel mechanism from user-space policy:

- the kernel module owns device state, synchronization, event notification, and validation;
- the shared UAPI header defines only stable values visible to user space;
- the C utility provides a small reference client;
- the Python harness executes repeatable functional, boundary, concurrency, and failure tests;
- hardware-specific GPIO and IRQ code remains isolated from the software-only core.

## Planned data flow

1. A user process opens the device node.
2. `read` and `write` exchange bounded data with the driver.
3. `ioctl` performs versioned control operations that do not fit stream I/O.
4. `poll` reports when an event or readable data is available.
5. The driver wakes waiting processes when its state changes.
6. The Python harness records the request, result, duration, and relevant logs.

## Planned kernel components

| Component | Responsibility |
|---|---|
| Module lifecycle | Allocate/register resources and unwind safely on failure |
| Device interface | Implement `open`, `release`, `read`, `write`, `unlocked_ioctl`, and `poll` as needed |
| Buffer/state | Hold bounded device data and counters |
| Synchronization | Protect shared state in process and interrupt contexts |
| Wait queue | Support blocking I/O and readiness notification |
| IRQ adapter | Later convert a verified hardware interrupt into a driver event |

## ABI principles

- Keep shared structures fixed-width, documented, and versioned.
- Validate command numbers, sizes, pointers, and ranges.
- Return standard negative error codes from the kernel.
- Never expose kernel pointers or padding accidentally.
- Treat backward compatibility as a design decision, not an assumption.

## Concurrency plan

The correct primitive will be selected from the actual access context. A mutex may protect process-context state that can sleep; interrupt-shared state may require a spinlock or an atomic operation. No locking choice is considered final until the implemented call paths are reviewed and tested.

## Hardware boundary

GPIO pin numbers, IRQ identifiers, trigger modes, register addresses, and device-tree entries will not be invented. They must come from the exact board, SoC, kernel, and binding documentation used during implementation.
