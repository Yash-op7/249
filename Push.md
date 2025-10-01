I want it as soon as possible

If you really want the results of a request immediately in the client, then push is one of the famous patterns to implement in the backned.

### Request/response isn't always ideal
- When client wants real-time notification from the backend
	- A user just logged in
	- A message is just received
- Push model is good for certain cases

### What is Push
- Client connects to the server
- Server sends data to the client
- Client doesn't have to request anything
- Protocol must be bidirectional
- Used by [[RabbitMQ]]
	- When you submit a message to the queue, their are clients that consume this queue, so the model that rabbit mq chooses as opposed to kafka is push

### Example
- First client and server establish a bidirectional connection
- then if backend wants to send any message at any time it can
- but this model is not scalable, because we cannot maintain all the connections all the time (Youtube example of push notifications for 100M subscribers)
- gRPC supports it in a unidirection way
- Client doesn't request anything, it just gets data
### Push Pros & Cons
- Pros
	- Real time
- Cons
	- Clients must be online/connected
	- Clients might not be able to handle, this is the reason Kafka doesnt use a push model like RabbitMQ, the client might get overloaded and crash thats why Kafka moved to long polling 
	- Requires a bidirectional protocol
	- Polling is preferred for light clients, the clients poll at their leisure (when they can handle the load)
### Implementation example:
- Simple Websocket server and clients (Group chat example)