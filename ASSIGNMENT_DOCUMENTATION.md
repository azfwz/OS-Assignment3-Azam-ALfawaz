
---

# Assignment 3 - Complete Documentation

**Student Name:** Azam Abdulaziz Alfawaz
**Student ID:** 445050167
**Date Submitted:** May 1, 2026

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)
**Video Link:** https://drive.google.com/file/d/115sZfc8cWWXXiynV4m61x479TgBRCYww/view?usp=drivesdk
**Video filename:** 445050167_Assignment3_Synchronization.mp4

**Verification:**
- [x] Link is accessible (tested in incognito mode)
- [x] Video is 3-5 minutes long
- [x] Video shows code walkthrough and commits
- [x] Video has clear audio
- [x] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

**Entry 1 - [April 30, 2026, 1:15 PM]**
*   **What I implemented:** Forked the starter repository, initialized the project in VS Code, and updated the student ID variable.
*   **Challenges encountered:** Identifying the specific shared variables that were causing the race conditions.
*   **How I solved it:** I examined the `SharedResources` class and identified the counters and the `ArrayList` as critical areas.
*   **Testing approach:** Ran the initial un-synchronized code to observe inconsistent final results.
*   **Time spent:** 45 min.

**Entry 2 - [April 30, 2026, 5:00 PM]**
*   **What I implemented:** Applied Task 1 and Task 2 by adding separate `ReentrantLock` objects for each counter and the execution log.
*   **Challenges encountered:** Ensuring that locks are always released even if an exception occurs during execution.
*   **How I solved it:** I strictly implemented the `try-finally` pattern for every lock acquisition.
*   **Testing approach:** Checked the output to ensure `ConcurrentModificationException` was no longer being thrown.
*   **Time spent:** 1.5h.

**Entry 3 - [May 1, 2026, 11:30 AM]**
*   **What I implemented:** Integrated Task 3 using a Binary Semaphore to restrict CPU access to one process at a time.
*   **Challenges encountered:** Coordinating the semaphore between the main `run()` and the `runToCompletion()` methods.
*   **How I solved it:** Wrapped the execution logic of both methods with `acquire()` and `release()` calls.
*   **Testing approach:** Monitored the console to ensure process execution logs were orderly and consistent.
*   **Time spent:** 1h.

---

## Part 2: Technical Questions (1 mark)

**Question 1: Race Conditions**
In the original code, the first race condition occurs at `contextSwitchCount++`. Since this operation is not atomic, multiple threads can read the same value simultaneously, causing lost updates where the final count is lower than expected. The second race condition is in `executionLog.add()`. Because `ArrayList` is not thread-safe, concurrent additions lead to internal data corruption or the program crashing with a `ConcurrentModificationException`.

**Question 2: Locks vs Semaphores**
A `ReentrantLock` is a mutual exclusion tool used to ensure only one thread owns a specific resource at a time. I used it for the counters and the log because they require exclusive access. A `Semaphore` is a signaling tool; I used it with 1 permit to act as a gatekeeper for the CPU, simulating a single-core environment where only one process can execute its quantum at any moment.

**Question 3: Deadlock Prevention**
A deadlock is a situation where threads wait indefinitely for locks held by each other. I prevented this by using `try-finally` blocks to guarantee that every lock is released regardless of errors. Additionally, I avoided nested locking, which ensures the "Circular Wait" condition needed for deadlocks cannot occur.

**Question 4: Lock Granularity Design Decision**
I chose fine-grained locking by using separate locks for each counter. Since `contextSwitchCount`, `completedProcessCount`, and `totalWaitingTime` are independent of each other, separate locks allow threads to update different counters in parallel. This design provides better concurrency than a single "coarse-grained" lock, which would unnecessarily block threads and slow down the simulation.

---

## Part 3: Synchronization Analysis (1 mark)

**Critical Section #1: Counter Variables**
*   **Which variables:** `contextSwitchCount`, `completedProcessCount`, `totalWaitingTime`.
*   **Why they need protection:** To prevent data loss during simultaneous increment operations.
*   **Synchronization mechanism used:** Three separate `ReentrantLock` objects.
*   **Code snippet:**
    ```java
    contextSwitchLock.lock();
    try { contextSwitchCount++; }
    finally { contextSwitchLock.unlock(); }
    ```
*   **Justification:** Protects independent counters while allowing maximum parallelism.

**Critical Section #2: Execution Log**
*   **What resource:** `ArrayList<String> executionLog`.
*   **Why it needs protection:** To prevent crashes during concurrent access.
*   **Synchronization mechanism used:** `logLock` (`ReentrantLock`).
*   **Justification:** Ensures the integrity of the log entries during multi-threaded execution.

**Critical Section #3: CPU Semaphore**
*   **Purpose of semaphore:** To simulate a single-core processor by limiting concurrent execution.
*   **Number of permits and why:** 1 permit, making it a binary semaphore for exclusive CPU access.
*   **Where implemented:** In `Process.run()` and `Process.runToCompletion()`.
*   **Effect on program behavior:** It ensures that only one process is "executing" on the CPU at any given time.

---

## Part 4: Testing and Verification (2 marks)

**Test 1: Consistency Check**
*   **What I tested:** Stability of results over 5 consecutive runs.
*   **Testing procedure:** Used `java SchedulerSimulationSync` multiple times.
*   **Results:** Every run produced identical stats (e.g., exactly 28 context switches and consistent waiting times).
*   **Conclusion:** Synchronization successfully removed the non-deterministic behavior of the threads.

**Test 2: Exception Testing**
*   **What I tested:** Presence of `ConcurrentModificationException`.
*   **Results:** 0 exceptions thrown during stress tests.
*   **What this proves:** The `logLock` correctly serializes access to the execution list.

**Test 3: Correctness Verification**
*   **What I tested:** Final values vs. manual expectations.
*   **Expected values:** `completedProcessCount` must equal the initial number of processes.
*   **Actual values:** Match perfectly in all test scenarios.

---

## Part 5: Reflection and Learning

**What I learned about synchronization:**
Synchronization is the backbone of safe multi-threaded programming. I learned that identifying "Critical Sections" is the most important step before applying locks. Managing thread access to shared data like the `executionLog` showed me how easily a program can crash without proper protection. Furthermore, the `try-finally` pattern is essential to avoid deadlocks, which can be very difficult to debug otherwise.

**Real-world applications:**
*   **Online Banking:** Preventing two simultaneous transfers from exceeding the available balance.
*   **Inventory Management:** Ensuring two orders don't claim the last item in stock at the same time.

**How I would explain synchronization to others:**
"Think of a shared computer lab with only one printer. Synchronization is like a signup sheet or a key to the printer room. It ensures that only one person can print at a time, so their pages don't get mixed up with someone else's work."

---

## Part 6: GitHub Repository Information

**Repository URL:** https://github.com/azfwz/OS-Assignment3-Azam-ALfawaz
**Number of commits:** 7
**Commit messages:**
1. Set my student ID: 445050167
2. Task 1 (445050167): Added fine-grained locks for counters
3. Task 2 (445050167): Protected execution log with lock
4. Task 3 (445050167): Implemented Semaphore for CPU control
5. Task 4 (445050167): Completed Assignment Documentation
6. Final verification and code cleanup
7. Final update for video link and documentation

**Summary**
*   **Total time spent:** ~5.5 hours.
*   **Key takeaways:** 1. Fine-grained locking is efficient. 2. `try-finally` is mandatory. 3. Semaphores control resource limits.
*   **Most challenging aspect:** Designing the locks to be efficient (fine-grained) without increasing complexity.
*   **What I'm most proud of:** Achieving 100% deterministic results across all simulation runs.

**End of Documentation**
```v
