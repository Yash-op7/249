Request is taking a while, I'll check with you later

Short Polling is same as Polling

### When req/response isn't ideal
- A request takes long time to process
	- Upload a youtube video, a slow process
- The backend wants to send notification
	- A user just logged in
- Polling is a good communication style for some use cases
### What is short polling
- Client sends a request
- Server responds immediately with a handle (eg. jobId)
- Server continues to process the request
- Client uses that handle to check for status
- Multiple "short" request response as polls
- Breaking down one big request response into multiple small ones

### Pros and Cons
- Pros:
	- Simple
	- Good for long running requests
	- Client can disconnect, the server can just save the job on disk
- Cons
	- Too chatty, when there are many clients, not scalable, network congestion
	- network bandwidth, this is a critical resource in backend, when you deploy it to the cloud, you will get billed on network bandwidth, so if the backend and clients are too chatty, more bill
	- wasted backend resources, every check takes backend's time