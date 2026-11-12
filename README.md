# Pthreads Barrier Synchronization & Performance Analysis

A multi-threaded benchmarking tool developed in C++ using POSIX Threads (`pthreads`) to analyze, implement, and compare barrier synchronization techniques across two distinct phases of global array operations[cite: 2, 4, 5].

The program benchmarks two barrier approaches—**Busy-Waiting** and **Condition Variables**—evaluating CPU resource efficiency, thread state transitions, and numerical aggregation correctness[cite: 2, 4, 5].

---

## 📌 Computational Workflow

The program divides a global array of size $16 \times 10^6$ among $N = 8$ concurrent threads across two distinct execution stages separated by a synchronization barrier[cite: 2, 4, 5]:

1. **Phase 1 (Parallel Transformation):** Each thread squares every integer element within its partition ($A[i] \leftarrow A[i]^2$)[cite: 2, 4, 5].
2. **Synchronization Barrier:** All threads must reach and acknowledge this boundary before any thread proceeds[cite: 2, 4, 5].
3. **Phase 2 (Parallel Reduction):** Once released, each thread calculates its chunk's local sum and aggregates it into a shared global variable protected by a mutex[cite: 2, 4, 5].

---

## ⚙️ Synchronization Strategies

### 1. Busy-Wait Barrier (`busy_wait.cpp`)
- **Mechanism:** Threads increment a shared counter (`arrived_count`) guarded by a mutex, then poll the counter in an active spin-lock loop (`while (arrived_count < N)`)[cite: 2, 5].
- **Trade-offs:** Introduces extreme CPU core starvation and high power consumption due to continuous checking while waiting for slower threads[cite: 2, 5].

### 2. Condition Variable Barrier (`cond_var.cpp`)
- **Mechanism:** Threads update the counter within a mutex lock[cite: 4]. Arriving threads enter a suspended sleep state using `pthread_cond_wait()` (releasing the mutex automatically) until the final arriving thread triggers a wake-up using `pthread_cond_broadcast()`[cite: 2, 3, 4].
- **Trade-offs:** Near-zero CPU utilization during the synchronization phase with minimal scheduler overhead, allowing other system tasks to utilize computing resources efficiently[cite: 2].

---

## 🛠️ Prerequisites

- **C++ Compiler:** GCC (`g++`) $\ge 9.0$ or Clang (supporting C++11 standard)[cite: 2]
- **POSIX Threads:** `libpthread` support[cite: 2]

---

## 💻 Compilation & Execution

Both implementations should be compiled with `-O3` optimization and linked against `pthread`[cite: 2]:

### Busy-Wait Implementation
```bash
# Linux / macOS
g++ -O3 busy_wait.cpp -o busy_app -lpthread
./busy_app

# Windows (MinGW-w64)
g++ -O3 busy_wait.cpp -o busy_app.exe -lpthread
./busy_app.exe
```

### Condition Variable Implementation
```bash
# Linux / macOS
g++ -O3 cond_var.cpp -o cond_app -lpthread
./cond_app

# Windows (MinGW-w64)
g++ -O3 cond_var.cpp -o cond_app.exe -lpthread
./cond_app.exe
```

---

## 📊 Sample Output

```text
--- Condition Variable Cozumu Baslatiliyor ---
Sonuc: 64000000
Beklenen: 64000000
```
*(Array size: $16 \times 10^6$, Initial value: $2$, Transformed value: $4$, Total: $64 \times 10^6$)*[cite: 4, 5]
