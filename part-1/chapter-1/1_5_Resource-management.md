# Resource Management — Silberschatz §1.5

> **Chapter/Section:** Silberschatz Ch. 1, §1.5
> **Date:** 2026-07-17
> **Status:** 🟡 In Progress

---

## 1. Quick Summary (TL;DR)
- The OS is a resource manager for CPU, memory, file-storage space, and I/O devices.
- Process management treats the process (an active instance of a program) as the unit of work; the OS creates, schedules, suspends, syncs, and communicates between processes.
- Memory management keeps several programs in memory and tracks/allocates/deallocates it; file-system management abstracts physical media into logical files and directories; mass-storage management handles secondary (HDD/NVM) and tertiary (tape/optical) storage.
- Caching copies data into faster storage temporarily (cache coherency matters in multiprocessor/distributed systems); the I/O subsystem hides device peculiarities via device drivers and buffering/caching/spooling.

---

## 2. Core Definitions

| Term | Definition |
|---|---|
| Process | An instance of a program in execution; an active entity (unit of work). Contrast with a passive program. |
| Single-threaded process | Has one program counter; execution is sequential, at most one instruction executed at a time. |
| Multithreaded process | Has multiple program counters, each for a given thread. |
| Main memory | Large array of bytes, directly addressable by the CPU; repository of quickly accessible data shared by CPU and I/O. |
| File | Logical storage unit abstracting physical media; a collection of related information defined by its creator. |
| Secondary storage | Principal on-line storage (HDDs, NVM) backing main memory; programs/data stored until loaded. |
| Tertiary storage | Slower, lower-cost, higher-capacity storage (magnetic tape, CD/DVD/Blu-ray) for backups/archival. |
| Cache | Faster temporary storage holding copies of information from a slower storage system. |
| Cache coherency | Ensuring an update to a value in one cache is immediately reflected in all other caches holding it (hardware issue in multiprocessor systems). |
| I/O subsystem | Component hiding device peculiarities; includes memory-management (buffering/caching/spooling), device-driver interface, and specific device drivers. |
| Device driver | Software that knows the peculiarities of a specific hardware device. |

---

## 3. Textbook Content

### 3.1 Process Management (§1.5.1)
- A program does nothing until executed by a CPU; a program in execution is a process (compiler, word processor, mobile app are all processes).
- A process needs resources (CPU time, memory, files, I/O), allocated while running; initialization data (input) may be passed (e.g. URL to a browser process). On termination, OS reclaims reusable resources.
- Program ≠ process: a program is passive (contents on disk); a process is active.
- Single-threaded: one program counter, sequential execution. Two processes from the same program are separate execution sequences. Multithreaded: multiple program counters (one per thread).
- A system is a collection of processes: OS processes (system code) and user processes (user code); they execute concurrently (multiplexed on one core) or in parallel (across cores).
- OS responsibilities: creating/deleting processes; scheduling processes/threads on CPUs; suspending/resuming; process synchronization; process communication. (Ch. 3–7.)

### 3.2 Memory Management (§1.5.2)
- Main memory central: CPU fetches instructions and reads/writes data from it (von Neumann); only large storage the CPU addresses directly. Disk data must be transferred to memory via I/O calls before processing.
- For execution, a program is mapped to absolute addresses and loaded into memory; it accesses instructions/data via those addresses; on termination its space is freed.
- To improve CPU utilization and response, several programs stay in memory → need for memory management; schemes depend on hardware and situation.
- OS responsibilities: tracking used memory and which process uses it; allocating/deallocating space; deciding what moves into/out of memory. (Ch. 9–10.)

### 3.3 File-System Management (§1.5.3)
- OS provides a uniform, logical view of storage, abstracting physical media into files mapped onto physical media.
- Storage media: secondary (most common), tertiary; differ in access speed, capacity, transfer rate, access method (sequential/random).
- File = collection of related info (programs, data; free-form or rigidly formatted). Organized into directories. Multi-user access control (read/write/append) may apply.
- OS responsibilities: creating/deleting files and directories; primitives for manipulating files/directories; mapping files onto mass storage; backing up on stable storage. (Ch. 13–15.)

### 3.4 Mass-Storage Management (§1.5.4)
- Secondary storage (HDD/NVM) backs main memory; programs stored there until loaded, used as source/destination of processing.
- OS responsibilities: mounting/unmounting; free-space management; storage allocation; disk scheduling; partitioning; protection. Efficiency is central to system speed.
- Tertiary storage (tape, CD/DVD/Blu-ray) for backups, seldom-used data, archival; not crucial to performance but still managed (mount/unmount, allocate/free, migrate data). (Ch. 11.)

### 3.5 Cache Management (§1.5.5)
- Caching: copy information from slower storage into faster cache temporarily; check cache first, else use source and copy in (assuming reuse).
- Registers = high-speed cache for main memory (compiler/programmer handles register allocation/replacement).
- Hardware-only caches (instruction cache, data caches) are outside OS control.
- Limited cache size → cache management is a design problem; replacement policy matters (Ch. 10).
- Storage hierarchy (Fig. 1.14): registers (L1), cache (L2), main memory (L3), SSD (L4), magnetic disk (L5); managed by compiler/hardware/OS at different levels.
- Same data can appear at multiple levels (Fig. 1.15: A on disk → memory → cache → register). Updated copies differ until written back.
- Multitasking: must ensure processes obtain the most recently updated value.
- Multiprocessor: local caches per CPU → cache coherency (hardware-handled). Distributed: replicas across computers must be brought up to date (Ch. 19).

### 3.6 I/O System Management (§1.5.6)
- OS hides hardware peculiarities from the user; in UNIX, the I/O subsystem hides them from most of the OS.
- I/O subsystem components: memory-management (buffering, caching, spooling); general device-driver interface; specific device drivers (only driver knows device specifics).
- Discussed further in Ch. 12 (interfaces, device management, data transfer, completion detection).

---

## 4. My Own Understanding / Explained Simply
*(Section intentionally omitted per study preference — textbook-faithful notes only.)*

---

## 5. Additional Knowledge (Beyond the Textbook)
*(Section intentionally omitted per study preference — textbook-faithful notes only.)*

---

## 6. Diagrams / Visuals

```
Figure 1.14 — Storage hierarchy (condensed)
Level  Name              Size        Tech        Access(ns)  BW(MB/s)  Managed by
1      registers         <1KB        custom CMOS 0.25-0.5    20k-100k   compiler
2      cache             <16MB       CMOS SRAM  0.5-25       5k-10k     hardware
3      main memory       <64GB       CMOS SRAM  80-250       1k-5k      OS
4      solid-state disk  <1TB        flash       25k-50k     500        OS
5      magnetic disk     <10TB       magnetic    5,000,000   20-150     OS / disk or tape
```

```
Figure 1.15 — Migration of integer A from disk to register
 magnetic disk  ->  main memory  ->  cache  ->  hardware register
      A              A              A           A
 (copy exists at each level; updated only after write-back to disk)
```

```
I/O subsystem
 +------------------------------------------+
 | memory management: buffering/caching/spooling
 | general device-driver interface
 | specific device drivers (know hardware)
 +------------------------------------------+
```

---

## 7. Worked Examples / Calculations

**Cache lookup logic:**
1. Need data X → check cache.
2. Hit: use directly from cache.
3. Miss: fetch from source, copy into cache (assume reuse), then use.

**Storage hierarchy latency intuition (Fig. 1.14):**
- Register access ≈ 0.25–0.5 ns; main memory ≈ 80–250 ns; magnetic disk ≈ 5,000,000 ns.
- Each lower level is larger, slower, and cheaper, and is "backed by" the next level down.

---

## 8. Comparisons

| Criteria | Secondary Storage | Tertiary Storage |
|---|---|---|
| Examples | HDD, NVM (SSD) | Magnetic tape, CD/DVD/Blu-ray |
| Speed / cost | Faster, higher cost/GB | Slower, lower cost, higher capacity |
| Use | Active programs/data (online) | Backups, archival, seldom-used |
| Performance critical? | Yes (system speed hinges on it) | No (but still managed) |

| Criteria | Program | Process |
|---|---|---|
| Nature | Passive (file on disk) | Active (instance in execution) |
| Has PC? | No | Yes (one or more, if multithreaded) |

| Criteria | Single-threaded | Multithreaded |
|---|---|---|
| Program counters | One | Multiple (per thread) |
| Execution | Sequential | Concurrent threads |

---

## 9. ⚠️ Common Misconceptions / Things That Tripped me Up
- A program is NOT a process; the process is the active execution instance.
- Two processes can come from the same program but are separate execution sequences.
- Main memory is the only large storage the CPU addresses directly — disk data must first go through memory.
- Hardware-only caches (instruction/data caches) are outside OS control; software-controlled cache replacement is in Ch. 10.
- Cache coherency in multiprocessor systems is a hardware issue, not OS-level.
- Registers act as a cache for main memory, managed by compiler/programmer, not the OS.
- Tertiary storage is not crucial to performance but is still often managed by the OS.

---

## 10. 🔑 Key Things to Remember
- [ ] OS manages CPU, memory, file storage, I/O.
- [ ] Process = active execution instance; program = passive.
- [ ] Process mgmt: create/delete, schedule, suspend/resume, sync, communicate.
- [ ] Memory mgmt: track, allocate/deallocate, decide what moves in/out.
- [ ] File = logical abstraction over physical media; organized in directories.
- [ ] Secondary (HDD/NVM) backs memory; tertiary (tape/optical) for archival.
- [ ] Cache: fast temp copy; coherency matters in multiprocessor/distributed.
- [ ] I/O subsystem hides hardware via device drivers + buffering/caching/spooling.

---

## 11. ❓ Possible Exam / Test Questions

1. **Q:** Why is a program not the same as a process?
   **A:** A program is a passive entity stored on disk; a process is an active instance in execution with resources and (one or more) program counters.

2. **Q:** What are the OS responsibilities in memory management?
   **A:** Track used memory and owning processes; allocate/deallocate space; decide which processes/parts move into and out of memory.

3. **Q:** What is cache coherency and when does it matter?
   **A:** Ensuring a value updated in one cache is reflected in all caches holding it; critical in multiprocessor (and distributed replica) environments; hardware-handled in multiprocessor.

4. **Q:** Contrast secondary and tertiary storage.
   **A:** Secondary (HDD/NVM) is fast online storage backing memory and critical to performance; tertiary (tape/optical) is slower/cheaper for backups and archival, less performance-critical.

5. **Q:** What components make up the I/O subsystem?
   **A:** Memory management (buffering, caching, spooling), a general device-driver interface, and specific device drivers.

6. **Q:** Why must the OS manage caching carefully in a multitasking system?
   **A:** The same data may exist at multiple hierarchy levels; processes switched among CPUs must obtain the most recently updated value.

---

## 12. 🔗 Links Between Topics
- Relates to → **§1.4 (Multiprogramming/Multitasking, Dual Mode)**: processes, concurrency, protection.
- Relates to → **Ch. 3–7 (Process Management)**: creation, scheduling, sync, communication, threads.
- Relates to → **Ch. 9–10 (Memory & Virtual Memory)**: allocation, replacement, caching.
- Relates to → **Ch. 11 (Mass Storage)**: secondary/tertiary management, disk scheduling.
- Relates to → **Ch. 12 (I/O Subsystem)**: device drivers, buffering, completion.
- Relates to → **Ch. 13–15 (File Systems)**: files, directories, mapping.
- Relates to → **§1.3 (Architecture, Fig 1.8)**: per-CPU local caches → coherency.
- Relates to → **Ch. 19 (Distributed Systems)**: replica consistency.

---

## 13. Sources
- Textbook: Silberschatz, Galvin, Gagne — *Operating System Concepts*, Ch. 1 §1.5, pp. 27–33 (includes Fig. 1.14 storage hierarchy and Fig. 1.15 migration).
- Lecture slides: (to be added)
- Other: (none — textbook-faithful only)

---

## 14. Self-Test Checklist
- [ ] I can define all key terms without looking
- [ ] I can explain this topic out loud without notes
- [ ] I can solve a calculation/example from scratch
- [ ] I know how this compares to related concepts
- [ ] I've reviewed common misconceptions
