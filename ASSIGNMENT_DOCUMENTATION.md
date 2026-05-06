# Assignment 3 - Complete Documentation

**Student Name**: [sarah abdulaziz alghanam]  
**Student ID**: [445052095]  
**Date Submitted**: [Submission Date]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [29 apr, 6 pm]
**What I implemented**: i Forked the repository renamed it and added the code file in vs 

**Challenges encountered**:Identifying the correct repository and understanding the setup instructions

**How I solved it**:Followed the "Quick Start Guide" in the assignment pdf because i still remember some of the steps in ass 1

**Testing approach**: i run the code to make sure my std id is showing 

**Time spent**: 1h 

---

### Entry 2 - [30 apr, 10 pm]
**What I implemented**:cloned my fork to my laptop and opened the project in vs to make sure everything is linked

**Challenges encountered**:vs at first did not open the solution file correctly and some files were not loaded

**How I solved it**: closed vs reopened the folder and selected the correct solution file then waited for restore to finish

**Testing approach**:built the project once to see if there are any errors in references

**Time spent**: 1 hour

---

### Entry 3 - [1 may , 3 am]
**What I implemented**:added my own function for the first task and connected it where it should be called in the code

**Challenges encountered**:was confused where exactly to put my function and which file is the right place 

**How I solved it**:checked the comments in the code and compared with the pdf to see which part matches my task

**Testing approach**:ran the code with simple input to see if my function is reached and gives any output

**Time spent**: 1:15 min 

---
### Entry 4 - [2 may, 7pm]
**What I implemented**:completed the second and third tasks and made sure they are connected in the right place in the code

**Challenges encountered**:did not know exactly where to add the new code for these tasks in the project

**How I solved it**:checked the pdf and the comments in the files and moved the code until it matched the right section

**Testing approach**:trying diffrent placeses untill its worked 

**Time spent**:2h 

---

### Entry 5 - [3 may , 2am]
**What I implemented**:cleaned up my code a bit and finished the last task

**Challenges encountered**:I put a brace in the wrong place and it broke the code

**How I solved it**:i tracked where the wrong brace is then tried another placess untill its worked 

**Testing approach**:ran the program after each change to see if it still runs and if the last task gives the right output

**Time spent**: 2:30 h

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[the first race shared resource the counters contextSwitchCount and completedProcessCount concurrent access loses increments and final counts become wrong
the second race shared resource the execution log arraylist concurrent add and iterate can corrupt the list and cause concurrentmodificationexception or missing log entries]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[reentrantlock is a mutual exclusion lock i used it to protect the counters and the execution log so each update happens as one atomic critical section semaphore is a permit based synchronization tool i used a binary semaphore before the cpu section so at most one process thread can execute on the simulated cpu at any time]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[deadlock is when threads wait forever for each other locks and nobody can continue and i prevent it by always releasing locks and semaphores in finally blocks so resources are never held forever and by never holding more than one lock at the same time]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[i used one lock for all three counters coarse grained locking makes the code simple and safe but threads updating different counters still block each other
using three separate locks fine grained would allow higher concurrency because independent counters could be updated in parallel but the design becomes more complex and easier to get wrong]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**:contextSwitchCount and completedProcessCount and totalWaitingTime

**Why they need protection**:many process threads update these counters so without the locks some updates can be lost 

**Synchronization mechanism used**:three reentrant locks contextSwitchLock completedProcessLock waitingTimeLock in class sharedresources near the top of the file

**Code snippet**:
```java
 public static final ReentrantLock contextSwitchLock = new ReentrantLock();
    public static final ReentrantLock completedProcessLock = new ReentrantLock();
    public static final ReentrantLock waitingTimeLock = new ReentrantLock(); 

public static void incrementContextSwitch() {
        contextSwitchLock.lock();
        try {
            contextSwitchCount++;
        } finally {
            contextSwitchLock.unlock();
        }
    }
    //method to increment completed process counter with lock protection
    public static void incrementCompletedProcess() {
        completedProcessLock.lock();
        try {
            completedProcessCount++;
        } finally {
            completedProcessLock.unlock();
        }
    }
    //method to add waiting time with lock protection
    public static void addWaitingTime(long time) {
        waitingTimeLock.lock();
        try {
            totalWaitingTime += time;
        } finally {
            waitingTimeLock.unlock();
        }
    }
```

**Justification**:each update is inside lock and unlock so only one thread changes a counter at a time and the final totals are correct

---

### Critical Section #2: Execution Log

**What resource**: the shared list executionLog in sharedresources

**Why it needs protection**:all process threads add log messages so without the lock the list could be modified while another thread is iterating and cause concurrent modification exception

**Synchronization mechanism used**:reentrantlock logLock in sharedresources used in logExecution 

**Code snippet**:
```java
 public static final ReentrantLock logLock = new ReentrantLock();
  public static List<String> executionLog = new ArrayList<>();

 //method to log execution with lock protection
    public static void logExecution(String message) {
        logLock.lock();
        try {
            executionLog.add(message);
        } finally {
            logLock.unlock();
        }
    }

```
**Justification**:the log lock makes every add call atomic so the list stay consistent and the program can without errors print the log at the end

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: to limit cpu access so that only one process thread can execute on the simulated cpu at a time

**Number of permits and why**:one permit binary semaphore so it behaves like a single core cpu and avoids overlap between process threads 

**Where implemented**:cpuSemaphore is declared in sharedresources and after that acquired and released in process run and runToCompletion methods near the top of each method 

**Code snippet**:
```java
     // Semaphore to limit CPU access aka cpu control 
    public static final Semaphore cpuSemaphore = new Semaphore(1);
 // This ensures only allowed number of processes run simultaneously
          try {
            SharedResources.cpuSemaphore.acquire();
        try {
            if (startTime == -1) {
              startTime = System.currentTimeMillis();
            }
        // Increment context switch counter
            SharedResources.incrementContextSwitch();
// the rest of the code
    } finally {
        SharedResources.cpuSemaphore.release();
    }
} catch (InterruptedException e) {
    System.out.println(Colors.RED + "  ✗ " + name + " was interrupted." + Colors.RESET);
}
public void runToCompletion() {
  // TODO: Similar synchronization needed here
        try {
            SharedResources.cpuSemaphore.acquire();
  try {
        // the rest of the code
    } finally {
        SharedResources.cpuSemaphore.release();
    }
} catch (InterruptedException e) {
    System.out.println(Colors.RED + "  ✗ " + name + " was interrupted." + Colors.RESET);
}
```

**Effect on program behavior**:only one process uses the cpu section at a time so context switches waiting time and progress bars are clean and results are the same every run 
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: running schedulersimulationsync main several times to see if statistics are consistent

**Testing procedure**:
in terminal compile once then run at least five times
```bash
# javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
(for all runs the total context switches completed processes and average waiting time stayed the same for the same student id)

**Why synchronization is necessary**: 
(without the locks on counters and log and without the cpu semaphore threads could overlap and some updates would be lost so the totals would change between runs)

**Conclusion**:the repeated identical outputs show that the synchronization is working correctly
---

### Test 2: Exception Testing
**What I tested**:checking that no concurrentmodificationexception happens when many threads write to executionlog
**Testing procedure**:run the program many times and watch the console for exceptions while it prints the process summary and execution log summary 

**Results**:no concurrent modification exception appeared and total log entries printed correctly every time 

**What this proves**:the log lock is protecting the shared list from unsafe concurrent modifications 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)
that final statistics match the process list for example number of completed processes equals numProcesses and average waiting time matches totalwaitingtime divided by processes size

**Expected values**:completed processes should equal the number of processes created and average waiting time should be totalWaitingTime divided by numProcesses 

**Actual values**: 

**Analysis**:expected and actual values are the same so counters and waiting time calculations are correct 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
