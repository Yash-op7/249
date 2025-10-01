> Request is taking long, I'll check with you later, but talk to me only when it's ready

- Same as polling, but the server doesn't respond immediately if the response is not ready yet, instead it blocks the client request, basically the server makes an async polling request to check job status and moves on to do other work, in the backend if the job is not done yet, the server wont respond immediately so the client's request is blocked


### Where req/resp & polling dont work (aren't ideal)
- A req takes long time to process
	- Uploading a youtube video
- The backend wants to send notifications
	- A user just logged in
- Short Polling is good but too chatty and not scalable
- Meet Long Polling, used by [[Kafka]]
- For clients to consume a topic in kafka the consumer will do a long polling request and the broker (kafka server) wont respond immediately, until there's an entry/message in the topic it will then respond back.

### What is Long Polling?
- Client sends a request
- Server responds immediately with a handle (jobId)
- Server continues to process the request
- Client uses that handle to check for status
- Server DOES NOT reply until it has the response
- So we got a handle, we can disconnect and we are less chatty, saving bandwidth
- In kafka if a consumer requests for a message in a topic, and there is no message yet, kafka blocks and doesnt' reply until no message is published to the topic, when a message is available, kafka writes to the response of the client's original request.
- Push didnt work for kafka because we cant know if the client can handle what the server is pushing at that time, so we use long polling to let the client poll at their leisure
- Some variations have timeouts too, on client or server side
- Its basically like a long request response, but the advantage is that the client can disconnect
### Long Polling Pros & Cons
- Pros
	- Less chatty and more backend friendly (because backend systems dont like to be chatted to a lot)
	- Client can still disconnect
- Cons
	- Not real time