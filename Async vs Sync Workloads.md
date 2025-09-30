> Can i do work while waiting?

### Synchronous I/O
 - Caller sends a request and blocks
	 - Used in the beginning of soft dev, old days
	 - example: client browser is blocked so the UI is uninteractable (buttons example)
	 - Request and block
	 - In the OS if a process is blocked, the OS does context switching and removes the process, the process was getting generous execution time but as soon as it does I/O it gets kicked out if process does sync I/O
 - Caller cannot execute any code meanwhile
 - Reciever responds caller unblocks
 - Caller and Receiver are in sync
 #### Examples of OS sync I/O
 - Program asks OS to read from disk
 - Program main thread is taken off the CPU
 - Read completes, program can resume exec
 - Great example of why sync is bad, consider this code:
 ```js
 5 doWork();
 6 readFile('largeFile.txt');
 7 doWork2();
```
- When the process comes to line 7 it requests the OS kernel to read a file, the kernel then talks to the driver which talks eventually to the disk controller which can be SSD or Hard drive and then sends a request to the device, which does the reading and the kernel just waits there for a response from the device (SSD), so neither the kernel is doing any work nor the application process is doing any work, this is wasted CPU execution time which can be used by other process thats why the OS kernel doesnt wait, it switches context. and thats why the program would also prefer async io so that it doesnt block.
### Aynchronous I/O
- Caller sends a request
- Caller can work until it gets a response
- How will the caller know if it got a response or not, 2 ways:
	- Checks repeatedly if the response is ready - node js
	- Receiver calls back when its done - linux and windows, use a completionist queue, bunch of ports
	- Spins up a new thread that blocks - node js, main thread is unblocked, the OS removes the new thread from the CPU when it reads
- Caller and receiver are not in sync

#### Example of an OS async call in Node js
- Program spins up a secondary thread
- Secondary thread reads from disk, OS blocks it
- Main program still running and executing code
- Node js by default spins up 4 worker threads by the **libuv** library (these threads are not spin up on demand, node js has them running already so they are hot and on standby) and configurable but the number of threads should not exceed the cpu, but we can do some optimization for example if we are I/O bound not CPU bound then we can configure more number of worker threads than the cpu cores
- Thread finishes reading and calls back main thread, example:
```js
readFile("largeFile.txt", onReadFinished(theFile));
```
the onReadFinished function will be called out of the blue whenever the thread is done.

### Sync vs Async in Request Response
- Synchronicity is a client property
- Most modern client libraries are async

### Sync vs Async in real life
- Sync
	- Asking someone a question in a meeting
- Async
	- Asking someone a question in an email

### Asynchronous workload is everywhere
- Asynchronous Programming (promises, async/await in js/futures in c++), where we mark a function as async and we move on with our execution so the process is not technically blocked
- Asynchronous backend processing
	- Lets say the client is sending processing workloads to the server, the server can queue the heavy computation and return a jobId to the client, so client sends a job, the backend queues it and sends a job id which the client can save on disk, now the client can disconnect and check the status at a later date using the job id
	- Normally the backend executes the request immediately even if the client is not blocked by that request, because from the backend's POV somebody is waiting for the response, so this is sync backend processing
-  Asynchronous commits in postgres
	- What is a commit, when we begin a transaction, where we do several changes to several tables, all the changes are being recorded in something call a **wall** or a write ahead log which is like a journal of changes, there is another data structure called pages where the actual data is in actual tables, postgress writes to both, but both of these are in memory up until you say commit, usually to commit the wall is enough for a commit even if the pages are in memory. The goal of the commit is to unblock the client, because the commit is **Synchronous** for the client in postgres, so the client will be blocked until the wall is written successfully/unsuccessfully to **disk**, this is a synchronous commit, 
- Asynchronous IO in Linux
	-  event polling (epoll) v/s io_uring
- Asynchronous Replication in DBs
	- Replication is the idea of having a primary writer in the Database and lots of secondary worker databases that are for read, so if we write to the primary database by beginning a transaction and we write, write write insert insert insert, these updates will be streamed to the replicas as we are writing, but then when we commit, there are 2 ways to do it:
	- 1. Synchronous Replication Commit: If you issue a commit, the main transaction in the primary doesn't actually commit, it will send commits to all the secondaries, and this is where two faced commits and three faced commits and paxos etc comes into the picture, and only if all of them or the majority of replicas commit successfully, then the primary commits, so the client is blocked while it waits, 
	- 2. Async commits: The client says to the primary only you matter, just commit and return and asynchronously in the backend just issue the commits to the replicas, but i dont care about the replicas committing successfully, we care only about the primary committing successfully, 
- Asynchronous OS fsync (fs cache):
	- when you write a file in your OS, it doesnt actually go to disk immediately, it stays in OS fs cache in form of pages, and then asynchronously the OS waits for enough pages to pile up and then it batches all the updates and writes them together instead of writing every small update to disk, but there are durability issues to this which database people dont like so this OS fsync is a flag that can be set on or off to skip fs cache.