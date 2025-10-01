- Style of storing state within an application and relying on those states to be there otherwise the logic breaks
- This can be taken and applied to a backend, a function, a protocol


## Stateful vs Stateless
- Stateful
	-  Stores state about client in its memory
	- Depends on the information being there (this is the main problem)
- Stateless
	- Client is responsible to "transfer the state" with every request
	- May store something but can safely lose it


### Stateless backends
- Stateless backends can still store data somewhere else
- Can you restart the backend during idle time while the client workflow continues to work.

### What makes a backend stateless
- Stateless backends can store state somewhere else (like a database)
- The backend remains stateless but the system is stateful
- Can you restart the backend during idle time and the client workflow continue to work, think about executions backend and the executions registry

![[Pasted image 20251001174135.png]]
![[Pasted image 20251001174145.png]]
![[Pasted image 20251001174151.png]]

- For stateful client and server you will need sticky load balancing (session token or other info in memory)
- For stateless backend you will have a DB read on every request (verifying session token)

### Stateful vs Stateless protocols
- Talking about the protocols itself, the way we talk between client and server
- The Protocols can be design to store or not store (not rely on) state
- [[TCP]] is stateful, because there is information stored about the client and the server in both the client and the server, there are also sequences, every segment sent is labeled with a sequence, and the sequence is actually stored in the state, there is a state diagram saying the connection is now closed, open, established, waiting, there is a state machine living in the client and server, and they maintain the connection, maintain the sequences, maintain the window sizes, the flow control, the congestion control, the congestion control window, all of this is state information if any of the state is lost, the connection is effectively useless
	- more formal terms: TCP is stateful
		- Sequences, Connection file descriptor
- [[UDP]] is stateless, its message based it doesnt store anything, even if it does store (like the file descriptor), its not sticky
	- example: [[DNS]], a stateless protocol, DNS sends queryID in UDP to identify queries, because when you send out a UDP datagram that has the DNS query you dont send it on a connection because UDP is connection-less, you simply say, hey I am going to this IP address, and this is the destination port, and you put your local port and IP address, and just send it, and since there is no connection we dont know which response to a UDP datagram is for which UDP request, thats why we use a queryID in DNS, (TCP has connections, so any response on the connections means the response is for this request), the DNS server is responsible once the reply comes saying that google.com is this IP here is the queryID this response is sent back to the port which actually sent the DNS resolution request, in this case the query id will come back and the UDP client will read the information and effectively the Linux OS will based on the destination port map it back to the application. But if the application dies then there is no one to accept this datagram, because the destination port no longer exists in the OS, so it can be argued that the DNS client-server is stateful but the protocol, which is UDP is stateless. 
	- [[QUIC]] sends conenctionID to identify connection, but QUIC is stateful, acts like TCP, but because it uses UDP which is stateless, it has to always send the same connectionID with every UDP packet