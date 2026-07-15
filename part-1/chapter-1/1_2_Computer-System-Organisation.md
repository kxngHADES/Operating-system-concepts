# Computer-System Organization
> Chapter 1.2 (Silberschatz, Galvin & Gagne — *Operating System Concepts*)
> **Date:** 2026-07-13
> **Status:** 🟠 In Progress
> **Pages:** 31 - 39 (Computer-System Organization)

---
## 1. Quick Summary (TL;DR)

Modern computer architecture is defined by the orchestrated interplay between the CPU and various device controllers, all competing for access to shared memory via a common system bus. To manage this efficiently, the system utilizes an interrupt-driven mechanism that allows the CPU to execute instructions in parallel with I/O operations, responding only when hardware signals an event. This entire operation is supported by a tiered storage hierarchy that balances the extreme speed of volatile registers and cache against the vast, permanent capacity of nonvolatile storage. These foundational components necessitate the precise definitions that follow, as they form the fundamental constraints within which every operating system must operate.

---

## 2. Core Definitions
|Term |Definition|
| --- | --- |
|System Bus | The primary communications path connecting the major components of a computer, including the CPU, memory, and device controllers.|
|Device Controller | Hardware responsible for managing a specific type of device, maintaining local buffer storage and a set of special-purpose registers.|
|Device Driver | A software component that "understands" the device controller and provides the rest of the OS with a uniform interface.|
|Interrupt | A hardware-triggered signal sent to the CPU to alert it of an event, requiring an immediate transfer of execution to a service routine.|
|Interrupt Vector| A table of pointers (memory addresses) stored in low memory that allows the CPU to quickly index and find the correct service routine.|
|Interrupt Service Routine (ISR)|The specific software handler that is executed to address the cause of a particular interrupt.
|Volatile Storage| Memory that loses its contents when power is removed, such as RAM (Main Memory).
|Nonvolatile Storage (NVS) |A broad category of storage that retains data even when power is lost; it includes both mechanical and electrical (NVM) types.|

---

## 3. Textbook Content
The strategic necessity of a common bus architecture in modern computing cannot be overstated. By allowing multiple device controllers and the CPU to share a single communication pathway to memory, the system facilitates parallel execution—ensuring that the CPU does not remain idle while a disk controller retrieves data.
### 3.1 Hardware Operations
A general-purpose system functions through the concurrent interaction of CPUs, Device Controllers, and a Memory Controller.
* Cycle Stealing and Contention: Because the CPU and various controllers all share the same system bus, they must "compete" for memory cycles. This contention can lead to performance bottlenecks if not managed correctly.
* The Synchronizer: To prevent data corruption and ensure orderly access, the Memory Controller acts as the ultimate referee, synchronizing access to shared memory so that only one component writes or reads at a given moment.

### 3.2 The Interrupt Mechanism
The interrupt-driven I/O cycle is the heartbeat of hardware-software interaction. The hardware implementation process follows a sophisticated cycle of state management:

1. Raising: A device controller "raises" an interrupt by asserting a signal on the interrupt-request line.
2. Catching: The CPU hardware "catches" the interrupt by sensing the request line after executing every single instruction.
3. Dispatching: The CPU reads the interrupt number, indexes the interrupt vector, and dispatches execution to the corresponding ISR.
4. Clearing and Resuming: The ISR services the device and restores the saved state. Crucially, the "return from interrupt" involves loading the saved return address back into the Program Counter, allowing the CPU to resume the interrupted task exactly where it left off.

### 3.3 Storage Hierarchy
Storage is organized according to the fundamental trade-offs of speed, cost, and volatility.

* Primary Storage: Registers, Cache, and Main Memory. These are fast, semiconductor-based, and typically volatile.
* Secondary Storage (NVS): Hard-disk drives (HDDs) and Nonvolatile Memory (NVM). These provide large, permanent capacity but are slower than primary storage.
* Tertiary Storage: Magnetic tapes and optical disks used for backups and long-term archival.

> While the textbook provides the technical "what," the next section clarifies the "how" through simplified mental models.

---
## 4. My Own Undestanding


----
## 5. Additional Knowledge (Beyond the Textbook)

Theoretical concepts are manifested in specific ways in commercial hardware to ensure cross-compatibility and reliability.

* **Real-World Vectors:** On Intel-based processors, the interrupt vector is strictly mapped from 0 to 255. Vectors 0–31 are reserved for nonmaskable hardware events (e.g., divide-by-zero errors or page faults), while 32–255 are maskable interrupts used by peripheral devices.
* **Firmware and EEPROM:** Since RAM is volatile, systems require **EEPROM** (Electrically Erasable Programmable Read-Only Memory) for the bootstrap process. For example, an iPhone uses EEPROM to store unique serial numbers and hardware data that must persist even when the battery dies.
----

## 6. Diagrams / Visuals

<center>

![figure_1.4](/figures/figure_1.4_interrupt-driven_IO_cycles.png)

![figure_1.5](/figures/figure_1.5_intel_processor_event-vector_table.png)

![figure_1.6](/figures/figure_1.6_storage-device_hierarchy.png)

![figure_1.7](/figures/figure_1.7_how_modern_computer_system.png)

</center>

Logic Map: The Interrupt-Driven I/O Cycle

1. Device Driver → Loads controller registers to initiate I/O.
2. Device Controller → Moves data from device to its local buffer.
3. Controller → Raises an interrupt by signaling the system bus.
4. CPU → Checks interrupt line after current instruction execution.
5. CPU → Saves current state and dispatches to ISR via Interrupt Vector.
6. ISR → Services the device and clears the interrupt.
7. CPU → Loads saved address to Program Counter and resumes task.

---
## 7. Worked examples / calculations

**Example 1: Storage conversion**
**Task:** Convert 2 GB (Gigabytes) into bytes using the 1024n rule found in the textbook sidebar.

**Solution**
* **Step 1:** 1 GB = 1024^3 bytes
* **Step 2:** 1024 * 1024 * 1024 = 1_073_741_824 bytes
* **Step 3** 1_073_741_824 * 2
* Result = 2_147_483_648 bytes


**Example 2: Interrupt chaining**
**Scenario:** A system has 40 device handlers but the interrupt vector only has 10 available slots for device interrupts.

**Solution**
1. **Implement chaining:** Each of the 10 vector addresses points to the head of a linked list of handlers.
2. **Traverse:** When an interrupt is raised at a specific index, the CPU calls the first handler in the list.
3. **Search:** If that handler does not recognize the device, it passes control down the list until the correct handler "claims" and clears the interrupt.


----
# 8. Comparisons

**Storage types**
|Criteria | RAM (Main memory) | NVM (Flash/SSD) | Hard Disks (HDD) |
| --- | --- | --- | --- |
|Speed | Very High | Medium | Low |
|Volatility | Volatile | Nonvolatile | Nonevolatile |
|Technology | Semiconductor | Eletrical (NVM) | Mechanical |

**Interrupt Types Comparison**
Feature | Maskable Interrupt | Nonmaskable Interrupt|
| --- | --- | --- |
|Usage | Device service requests (Keyboard, Disk). | Critical errors (Memory failure). |
|Control | Can be deferred during critical code. | Cannot be ignored; must be handled. |
|Vector (intel) | Mapped to 32–255. | Mapped to 0–31. |


---

## 9. Miscomceptions

* The CPU moves data directly from the disk to main memory.
  * The Device Controller manages data movement between the device and its local buffer. The CPU only interacts with the buffer once the controller signals that the transfer is complete.
* The CPU stops mid-instruction to handle an interrupt.
  * The CPU checks the interrupt-request line only between instructions. An instruction is always completed before the context switch occurs.
* Main memory is the fastest storage in the system.
  * Registers and Cache are much faster than RAM, as they are located directly on or very near the CPU core.

---
## 10. 🔑 Key Things to Remember

- [ ] The Memory Controller is the critical bottleneck manager that synchronizes cycles between the CPU and I/O controllers.
- [ ] The Instruction Cycle check: The CPU senses the interrupt-request line after executing every instruction.
- [ ] Vectored Interrupts are used specifically for speed, allowing a direct jump to a handler without a generic search routine.
- [ ] Program Counter (PC) Restore: The return from interrupt specifically reloads the PC with the saved address of the next instruction.
- [ ] Storage Trade-off: As you move down the hierarchy, access time increases, capacity increases, and cost per byte decreases.

---
## 11. ❓ Possible Exam / Test Questions
Q: Why is an interrupt vector used instead of a single, generic interrupt handler? 
A: To minimize overhead. Because interrupts happen frequently, the CPU needs an indexed table to jump directly to the specific code required, bypassing the delay of a search routine.

Q: Explain the storage trade-off between mechanical and electrical NVS. 
A: Mechanical NVS (HDDs) offers massive capacity at low cost but is slowed by physical movement. Electrical NVS (NVM/Flash) is faster and more durable but significantly more expensive per byte.

Q: How does a maskable interrupt differ from a nonmaskable interrupt in terms of "critical sequences"? 
A: Maskable interrupts can be "turned off" (deferred) by the CPU during critical instruction sequences that must not be interrupted. Nonmaskable interrupts are reserved for unrecoverable errors and cannot be deferred.