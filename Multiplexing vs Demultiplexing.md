> HTTP/2, QUIC, Connection Pools, MPTCP

![[Pasted image 20251001165910.png]]

Multiplexing: Assume you are getting multiple requests/signals (three user events on the client) into a box, then the box shoves all the signals into a single line then it is called multiplexing, think of the diagram as 3 [[TCP]] connections ([[Connections in TCP]]), converted into 1 tcp connection so in this case you are multiplexing,

![[Pasted image 20251001174017.png]]
#### Multiplexing HTTP/2 on the Backend
![[Pasted image 20251001174026.png]]
- More throughput
- cost is more resource, because:
	- More work for the CPU because it needs to demultiplex
![[Pasted image 20251001174045.png]]
![[Pasted image 20251001174056.png]]
There are advantages and disadvantages to multi and demultiplexing
- Its really good to have each request in its own connection because it has its own rules like flow control and congestion control, and requests wont affect each other
- This limitation is not an issue in QUIC

### Connection Pooling
- A technique where you can effectively establish 'x' number of connections and keep them **hot** and when any client request comes in we pick a free connection and use it, so we have a pool of ready connections instead of setting up a connection on request arrival, reducing resource contention between requests, 

### see example of 6 max connections in chrome using long polling

- Chrome only allows upto 6 connections per domain, reusability is really important to chrome,