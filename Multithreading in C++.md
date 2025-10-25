## Syntax and Basics

---
## 🔢 Step 1: What is a thread?

A **thread** is a lightweight unit of execution that runs inside a process. Threads share memory and resources, unlike separate processes.

In C++, threads are supported via the **`<thread>`** header since C++11.

---

## 🔤 Step 2: Basic Thread Syntax

### ✅ 1. Include header:

`#include <thread>`

### ✅ 2. Define a function that the thread will run:

`void myFunction() {     // do some work }`

### ✅ 3. Start a thread:

```cpp
std::thread t1(myFunction); // t1 starts running myFunction
```

### ✅ 4. Wait for the thread to finish:

`t1.join();`

---
## 🔗 What does `.join()` mean?

`thread.join()` means:

> "Wait for this thread to finish before continuing."

### ⚠️ Why is `.join()` important?

If you **don’t** call `.join()` or `.detach()`, the destructor of `std::thread` will be called when the thread object goes out of scope. This leads to:

- **Runtime error**: `terminate called without an active exception`
    
- Because the thread is **still running**, and C++ doesn’t know what to do with it

### Example:

```cpp
std::thread t1(doSomething); t1.join();  // main thread will wait here until t1 is done
```

If you **don’t call `.join()` or `.detach()`**, your program will crash (terminate) at runtime.

---

## 🧵 `std::thread` Lifecycle
C++ doesn't expose low-level OS thread states like runnable, blocked, etc., directly. But conceptually:

OS Thread State	In C++
New	After std::thread is created
Runnable/Running	Once thread is started
Blocked	If thread is waiting (e.g., sleep, lock)
Terminated	After function ends

You don’t manage these states directly — the C++ runtime and OS handle them.

### how to share memory between threads

#### 💡 Global Variables?

You don’t **have to** use global variables in threads, but they’re often used in simple examples because:

- Threads share memory, so global or shared variables let them communicate or update data.
    
- In real applications, you’d typically pass **shared objects, pointers, or references**.


---

Lets see some simple C++ examples demonstrating **multithreading** using:

1. **Mutex**
    
2. **Semaphore**
    
3. **Atomic variables**

# 1. Mutex example

```css
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;
int counter = 0;

void increment() {
    for (int i = 0; i < 10000; ++i) {
        mtx.lock();           // lock the mutex before entering critical section
        ++counter;            // critical section: modify shared variable
        mtx.unlock();         // unlock mutex after critical section
    }
}

int main() {
    std::thread t1(increment);
    std::thread t2(increment);

    t1.join();
    t2.join();

    std::cout << "Final counter value: " << counter << std::endl;
    return 0;
}
```
**Explanation:**  
Two threads increment a shared variable `counter`. The mutex ensures only one thread updates the variable at a time, preventing race conditions.

##### 🔍 Flow:

1. `counter` is shared — both threads want to update it.
2. `mtx.lock()` is used to ensure only one thread increments `counter` at a time.
3. Without this, threads might clash and the final value would be incorrect (race condition).
4. `join()` ensures main thread waits until both threads finish.

## 2. Semaphore example

C++20 added `std::counting_semaphore`. If you don’t have C++20, you can use POSIX semaphores on Linux (`sem_t`). Here is a C++20 example:

```css
#include <iostream>
#include <thread>
#include <semaphore>
#include <vector>

std::counting_semaphore<3> semaphore(3);  // allows up to 3 threads concurrently

void worker(int id) {
    semaphore.acquire();  // acquire a key
    std::cout << "Worker " << id << " is working\n";
    std::this_thread::sleep_for(std::chrono::seconds(1)); // simulate work
    std::cout << "Worker " << id << " is done\n";
    semaphore.release();  // release the key
}

int main() {
    std::vector<std::thread> threads;
    for (int i = 1; i <= 6; ++i) {
        threads.emplace_back(worker, i);
    }

    for (auto& t : threads) {
        t.join();
    }
    return 0;
}
```
**Explanation:**  
The semaphore limits the number of threads inside the “working” section to 3. Even though 6 threads start, only 3 can “work” concurrently.

##### 🔍 Flow:

1. You create a semaphore with `3` "permits" (keys).
    
2. Only **3 threads at a time** can enter the work section.
    
3. `acquire()` waits if no permit is available.
    
4. When a thread finishes, it calls `release()` to let others in.

# 3. Atomic Var example

```css
#include <iostream>
#include <thread>
#include <atomic>

std::atomic<int> atomic_counter(0);

void increment() {
    for (int i = 0; i < 10000; ++i) {
        atomic_counter.fetch_add(1, std::memory_order_relaxed);
    }
}

int main() {
    std::thread t1(increment);
    std::thread t2(increment);

    t1.join();
    t2.join();

    std::cout << "Final atomic counter value: " << atomic_counter.load() << std::endl;
    return 0;
}
```
**Explanation:**  
Two threads increment the atomic counter safely without using mutex locks. The atomic variable ensures the increment operation is thread-safe.
##### 🔍 Flow:

1. `atomic_counter` is a special type that handles concurrency safely **without mutexes**.
    
2. `fetch_add(1)` increases the value **atomically**.
    
    - No two threads can interfere.
        
    - Internally uses CPU atomic instructions.
        
3. Safer and faster than mutex for simple updates.


#### Next -> See real world application example of a banking application in C++ using multithreading and OOPs in [[Multithreading]] using mutex, also an example of producer consumer pattern using `std::conditional_variable`
