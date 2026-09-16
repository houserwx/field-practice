# EtherCAT first-light gate (free)

Recursed Studios Field Practice · one page · 2026

Print this. If you fail a gate, stop. Do not tune software to rescue the wrong NIC.

## Control split

| Plane | Miss a deadline? | Lives on |
|---|---|---|
| Hard RT | No | Isolated CPU — PDO, safety, torque, heartbeat |
| Firm RT | Rarely | Second core — state machine, fusion that can drop a sample |
| Best effort | Yes | Companion / GPU — vision, UI, logs, gRPC |

If a thread allocates, logs, waits on a lock, or uses a blocking socket, it is not hard RT.

## Hardware

- [ ] Master NIC is Intel I210/I211/I225/I226-class, a trusted EtherCAT HAT, or a vendor adapter with a DC story — **not** the stock Realtek as the only port
- [ ] No consumer switch in the EtherCAT line
- [ ] Master NIC is not also the office LAN
- [ ] E-stop and safety power do not depend on Linux staying up
- [ ] Vision is a separate box or at least a separate NIC
- [ ] Dual-master heartbeat is a second link, not process data

## Linux

- [ ] PREEMPT_RT or vendor RT image — not Ubuntu Desktop
- [ ] Isolated cores + `SCHED_FIFO` 80–90 on the master thread
- [ ] EtherCAT NIC IRQs pinned
- [ ] Idle `cyclictest` on that core is boring (max << your cycle time)
- [ ] snapd, desktop, docker, unattended-upgrades off the RT core

```
cyclictest -p 90 -m -n -i 1000 -l 100000
```

## First light (in order)

1. Link — expected slave count
2. PREOP → SAFEOP
3. OP, outputs inhibited
4. DC locked and not walking
5. 1 hour soak, no motion, 0 late frames
6. One harmless output
7. One axis, limited current and travel
8. Start vision — histogram still boring
9. Unplug a slave, kill the companion — safety path wins

If step 8 is the first ugly histogram, you have a shared-resource problem, not a drive problem.

## Full sequence

The paid kit adds the log spec, dual-master split-brain rules, printable acceptance sheet, and the failure tree that eats weeks.

Email **recursedstudios@gmail.com** with subject `KIT` ($79) or `CLINIC` ($397).

https://github.com/houserwx/field-practice
