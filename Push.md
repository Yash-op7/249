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