# Computer-System Architecture — Silberschatz §1.3

> **Chapter/Section:** Silberschatz Ch. 1, §1.3
> **Date:** 2026-07-17
> **Status:** 🟡 In Progress

---

## 1. Quick Summary (TL;DR)
- Computer systems are categorized roughly by the number of general-purpose processors used: single-processor, multiprocessor, and clustered systems.
- A single-processor system has exactly one general-purpose CPU with a single core; special-purpose processors do not make it multiprocessor. Few contemporary systems qualify.
- Multiprocessor systems (SMP, multicore, NUMA, blade) dominate; they increase throughput but speed-up with N processors is less than N due to overhead and shared-resource contention.
- Clustered systems loosely couple two or more independent nodes (usually multicore) over a network to provide high availability, graceful degradation, and high-performance computing.

---

## 2. Core Definitions

| Term | Definition |
|---|---|
| CPU | The hardware that executes instructions. |
| Processor | A physical chip that contains one or more CPUs. |
| Core | The basic computation unit of the CPU. |
| Multicore | Including multiple computing cores on the same CPU. |
| Multiprocessor | Including multiple processors. |
| Symmetric Multiprocessing (SMP) | Each peer CPU processor performs all tasks, including OS functions and user processes; all CPUs share physical memory over the system bus. |
| Non-Uniform Memory Access (NUMA) | Each CPU (or group) has its own local memory accessed via a fast local bus; CPUs connected by a shared interconnect sharing one physical address space. |
| Clustered System | Two or more individual systems (nodes, typically multicore) joined together, loosely coupled, sharing storage and linked via LAN or faster interconnect. |
| Hot-Standby | A machine in standby mode that does nothing but monitor the active server; takes over if it fails. |
| Graceful Degradation | Ability to continue providing service proportional to the level of surviving hardware. |
| Fault Tolerant | Can suffer a failure of any single component and still continue operation. |
| Distributed Lock Manager (DLM) | Provides access control and locking so multiple hosts can access shared data without conflicting operations. |
| Storage-Area Network (SAN) | A pool of storage that many systems can attach to, enabling any host to run an application stored on it. |

---

## 3. Textbook Content

### 3.1 Single-Processor Systems
- Historically, most systems used a single processor with one CPU and a single core.
- The core executes instructions and holds local registers for data storage.
- The main CPU runs a general-purpose instruction set, including instructions from processes.
- Other special-purpose processors exist (device-specific): disk, keyboard, and graphics controllers.
  - They run a **limited instruction set** and **do not run processes**.
  - Some are managed by the OS (OS sends tasks, monitors status), e.g. a disk-controller microprocessor runs its own disk queue/scheduling, relieving the main CPU of that overhead.
  - Some are autonomous low-level hardware components the OS cannot communicate with (e.g. keyboard microprocessor converting keystrokes to codes).
- The presence of special-purpose microprocessors does **not** make a single-processor system into a multiprocessor.
- A system is single-processor **only if** there is one general-purpose CPU with a single core. Very few contemporary systems meet this.

### 3.2 Multiprocessor Systems
- Modern computers (mobile to servers) are predominantly multiprocessor.
- Traditional form: two or more processors, each with a single-core CPU, sharing the bus and sometimes clock, memory, and peripherals.
- **Primary advantage: increased throughput** — more work in less time.
- Speed-up ratio with N processors is **less than N** due to overhead of cooperation and contention for shared resources.
- **Symmetric Multiprocessing (SMP):** each peer CPU performs all tasks (OS + user processes).
  - Each CPU has its own registers and private (local) cache.
  - All processors share physical memory over the system bus.
  - N processes can run on N CPUs without significant deterioration, but idle/overloaded imbalance causes inefficiency; sharing data structures lowers workload variance (requires careful OS design — Ch. 5, 6).
- **Multicore:** multiple cores on a single chip. More efficient than multiple single-core chips because on-chip communication is faster and power consumption is lower (important for mobile/laptops).
  - Each core has its own register set and local L1 cache; an L2 cache is local to the chip but shared by cores.
  - Local lower-level caches are smaller and faster than higher-level shared caches.
  - A multicore processor with N cores appears to the OS as N standard CPUs, pressuring OS designers and programmers to use cores efficiently (Ch. 4).
  - All modern OS (Windows, macOS, Linux, Android, iOS) support multicore SMP.
- **Scaling limit:** adding too many CPUs causes system-bus contention → bottleneck, performance degrades.
- **NUMA:** each CPU/group gets its own local memory via a small fast local bus; CPUs connected by shared interconnect sharing one physical address space.
  - Local memory access is fast with no interconnect contention → scales better than SMP.
  - Drawback: latency accessing remote memory across interconnect (e.g. CPU0 slower to access CPU3's local memory).
  - OS minimizes penalty via careful CPU scheduling and memory management (§5.5.2, §10.5.4). Popular on servers and HPC.
- **Blade servers:** multiple processor/I/O/networking boards in one chassis. Each blade board boots independently and runs its own OS; some are multiprocessor. Essentially multiple independent multiprocessor systems.

### 3.3 Clustered Systems
- Gather multiple CPUs; composed of two or more individual **nodes** (typically multicore), loosely coupled.
- Nodes share storage and are closely linked via LAN (Ch. 19) or faster interconnect (e.g. InfiniBand).
- **Primary use: high-availability service** — continues even if one or more nodes fail.
  - Achieved via redundancy; cluster software monitors nodes over the network.
  - If a monitored machine fails, the monitor takes ownership of its storage and restarts its applications (brief interruption only).
- **Graceful degradation:** continued service proportional to surviving hardware.
- **Fault tolerant:** survives any single component failure; requires detection, diagnosis, and correction.
- **Asymmetric clustering:** one machine in hot-standby monitoring the active server; takes over on failure.
- **Symmetric clustering:** two or more hosts run applications and monitor each other; more efficient (uses all hardware) but needs multiple applications available.
- **High-performance computing:** clusters run applications concurrently across all nodes; requires parallelization (dividing a program into parallel components, combining results).
- **Parallel clusters:** multiple hosts access the same data on shared storage; need special software (e.g. Oracle Real Application Cluster) and a DLM for access control/locking.
- **Clustering over WAN** and large clusters (thousands of systems, miles apart) enabled by SANs (§11.7.4): applications/data on SAN → any attached host can run them; failed host's work taken over by another.

---

## 4. My Own Understanding / Explained Simply
*(Section intentionally omitted per study preference — textbook-faithful notes only.)*

---

## 5. Additional Knowledge (Beyond the Textbook)
*(Section intentionally omitted per study preference — textbook-faithful notes only.)*

---

## 6. Diagrams / Visuals

```
Figure 1.8 — Symmetric Multiprocessing (SMP)
+----------+        +----------+
| CPU 0    |        | CPU 1    |
| regs+L1  |        | regs+L1  |
+----+-----+        +----+-----+
     |                   |
     +-------- BUS -------+
              |
        [ Physical Memory ]
 (all CPUs share memory over system bus)
```

```
Figure 1.9 — Dual-Core on One Chip
+-----------------------------+
|  Core 0 (regs + L1)         |
|  Core 1 (regs + L1)         |
|                             |
|        Shared L2 cache      |
|        [ Single Chip ]      |
+-----------------------------+
        |
      [ System Bus ] -> Physical Memory
```

```
Figure 1.10 — NUMA Multiprocessing
 CPU0 -- memory0        CPU2 -- memory2
   |        |             |        |
   +---- interconnect ----+        |
 CPU1 -- memory1        CPU3 -- memory3
 (each CPU has local memory; remote access is slower)
```

```
Figure 1.11 — Clustered System (general)
 [computer] -- interconnect -- [computer] -- interconnect -- [computer]
                    |
              storage-area network (shared storage)
 (each node is typically a multicore system; loosely coupled)
```

---

## 7. Worked Examples / Calculations

**Concept — Speed-up ratio under multiprocessing:**
- With N processors, ideal speed-up = N (N times the work in same time).
- Actual speed-up < N because:
  1. Overhead of keeping cooperating parts correct.
  2. Contention for shared resources (bus, memory).
- Example: 4 CPUs will NOT give 4× throughput; gain is reduced by overhead + contention. As N grows large (especially in SMP), bus contention becomes a bottleneck and performance can degrade.

**NUMA latency intuition:**
- CPU0 accessing its own memory0: fast, no interconnect contention.
- CPU0 accessing memory3 (remote): slower across interconnect → possible performance penalty.

---

## 8. Comparisons

| Criteria | SMP | NUMA |
|---|---|---|
| Memory | Single shared physical memory over bus | Each CPU has local memory + shared address space |
| Local access | All memory equally shared | Local fast, remote slow |
| Scaling | Poor past many CPUs (bus bottleneck) | Scales better (local access avoids contention) |
| Drawback | Bus contention degrades with more CPUs | Remote-memory latency penalty |

| Criteria | Asymmetric Clustering | Symmetric Clustering |
|---|---|---|
| Host roles | One active, one hot-standby | All hosts run apps + monitor each other |
| Hardware use | Idle standby wastes hardware | Efficient (uses all) |
| Requirement | One application | Multiple applications available |

| Criteria | Single-Processor | Multiprocessor |
|---|---|---|
| General-purpose CPUs | Exactly 1 (single core) | 2 or more (or multicore) |
| Special-purpose processors | May exist, don't count | May exist |
| Prevalence today | Very few | Dominant |

| Criteria | Multiple single-core chips | Multicore (multiple cores on one chip) |
|---|---|---|
| Communication | Between-chip (slower) | On-chip (faster) |
| Power | Higher | Lower (mobile/laptop friendly) |

---

## 9. ⚠️ Common Misconceptions / Things That Tripped me Up
- Special-purpose processors (disk, keyboard controllers) do **not** make a system multiprocessor — only general-purpose CPUs with cores count.
- "Processor", "CPU", and "core" are distinct: processor = chip; CPU = instruction-executing hardware on chip; core = basic computation unit. A processor can hold multiple CPUs/cores.
- Multiprocessor historically meant multiple chips; now includes multicore (multiple cores on one chip).
- Adding more CPUs does not give linear (N×) speed-up; overhead and contention keep it below N, and too many CPUs in SMP causes bus bottleneck.
- "Symmetric" in SMP (all CPUs equal, share memory) is different from "symmetric clustering" (all nodes run apps and monitor each other).
- NUMA local vs remote memory access is not uniform in speed — hence "non-uniform".

---

## 10. 🔑 Key Things to Remember
- [ ] Single-processor = ONE general-purpose CPU, single core (special-purpose chips don't count).
- [ ] Multiprocessor advantage = increased throughput; speed-up < N.
- [ ] SMP: peer CPUs, private registers/cache, shared physical memory.
- [ ] Multicore: multiple cores per chip; appears to OS as N CPUs; L1 private, L2 shared.
- [ ] NUMA: local memory fast, remote slow; scales better than SMP.
- [ ] Blade servers: independent boards, each its own OS.
- [ ] Clusters: loosely coupled nodes, high availability via redundancy.
- [ ] Graceful degradation vs fault tolerant (survives any single failure).
- [ ] Asymmetric (hot-standby) vs symmetric clustering.
- [ ] DLM / SAN enable parallel clusters and failover.

---

## 11. ❓ Possible Exam / Test Questions

1. **Q:** According to the textbook, what defines a single-processor system, and why don't special-purpose processors change that?
   **A:** Exactly one general-purpose CPU with a single core. Special-purpose processors run a limited instruction set and do not run processes, so they don't count toward the general-purpose processor count.

2. **Q:** Why is the speed-up ratio with N processors less than N?
   **A:** Overhead of keeping cooperating parts correct, plus contention for shared resources (bus, memory).

3. **Q:** Contrast SMP and NUMA architectures.
   **A:** SMP shares one physical memory over a bus (bus becomes bottleneck as CPUs grow). NUMA gives each CPU local memory via a fast local bus, sharing one address space over an interconnect; local access is fast, remote access slower, but scales better.

4. **Q:** What is the difference between asymmetric and symmetric clustering?
   **A:** Asymmetric has one active server and one hot-standby monitoring it; symmetric has multiple hosts running applications while monitoring each other (more efficient).

5. **Q:** Define graceful degradation and fault tolerance.
   **A:** Graceful degradation = service continues proportional to surviving hardware. Fault tolerant = survives any single component failure and continues, requiring detection/diagnosis/correction.

6. **Q:** Why are multicore systems more efficient than multiple single-core chips?
   **A:** On-chip communication is faster than between-chip, and one multicore chip uses significantly less power.

---

## 12. 🔗 Links Between Topics
- Relates to → **Ch. 4 (Threads / Multicore)**: pressure on programmers/OS to use cores efficiently.
- Relates to → **Ch. 5 & 6 (CPU Scheduling / Synchronization)**: SMP requires careful OS design for shared data structures.
- Relates to → **Ch. 5.5.2 & 10.5.4 (NUMA scheduling & memory management)**: minimizing NUMA penalty.
- Relates to → **Ch. 11.7.4 (SAN)**: enables large/far clusters and failover.
- Relates to → **Ch. 19 (LAN/WAN)**: clustering interconnects (LAN, WAN, InfiniBand).

---

## 13. Sources
- Textbook: Silberschatz, Galvin, Gagne — *Operating System Concepts*, Ch. 1 §1.3, pp. 16–21.
- Lecture slides: (to be added)
- Other: (none — textbook-faithful only)

---

## 14. Self-Test Checklist
- [ ] I can define all key terms without looking
- [ ] I can explain this topic out loud without notes
- [ ] I can solve a calculation/example from scratch
- [ ] I know how this compares to related concepts
- [ ] I've reviewed common misconceptions
