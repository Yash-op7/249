> One Request, a very very long response

 - Basically, the client makes a http request, and the backend responds with a never ending response or a stream of messages, Content-Type: chunk/stream
 - The client parses the chunks from the stream
### Limitations of Req/resp
- Vanilla Req/resp isnt ideal for notification backend
- Client wants real time notificaiton from backend
	- A user just logged in
	- A message is just received
- Push model works but is restrictive
- SSE work with Req/resp model and works with any http server out of the box without needing a websockets server
- Designed for HTTP

### What is SSE?
- An HTTP response always has a start and an end
- Client sends a req
- Server sends logical events as part of response 
- So to the HTTP response it didnt technically end because the end marker(the final two lines that end the response) was not sent by the server, 
- But we are sending events in chunks that is a server sent object, event-source: object, in the browser which understands this, and the client can just parse the mini events and use them
- Server neveer writes the end of the response
- Its still a request but an unending response
- Client parses the streams data looking for events
- Works with request/response (HTTP)
# ⚠️ HTTP 1.0 doesn't support streaming, we need HTTP 1.1 for SSE

### Example:
1. The client sends a special request, with a special Content-Type:  
2. The server will respond with an event, which is a bunch of bytes that has a start and end (different from the start and end of an http resp) that the client needs to understand and parse the bytes in that event, the server hasnt finished writing the response because if you write a response you write a part of the response but you dont have to write a full response thats how tcp sockets work
3. the server can write more events and close the connection when done

### SSE Pros and Cons
 - Pros
	 - Real time
	 - Compatible with req/resp
 - Cons
	 - Clients must be online
	 - Clients might not be able to handle (same as push)
	 - Polling is preferred for light clients
	 - There is a limitation with HTTP/1.1, Chrome today if you connect it to a domain, you can only establish 6 TCP connections to that domain, thats a limit arbitrarily put by chrome and lot of browsers follow it, why, because in HTTP 1.1 you can only send one request per connection, and while that request is beign processed you cannot send anything else in that connection unless you enable pipelining but its a problematic thing so nobody does, so you can only send one request and the moment you send one reques the connection is marked as busy, and if a conn is marked as busy you cannot use it, so what about the rest of the HTTP request? i want my CSS, my JS, my other files, well thats why the browser opens upto 6 connections and you can use those to send requests, but the issue is what if all the 6 are SSE requests, then all 6 connections are marked as busy then other requests will starve, thats why HTTP 2 is preferred where we can have unlimited streams, we can send multiple streams in teh same connection, and the limit is like 200 and configurable, this is described better below:
### ⚡ The Core Issue

- In **HTTP/1.1**, **one TCP connection = one request at a time**.
- If a request is ongoing (like an SSE request which never ends), that connection is considered **busy**.
- Browsers (like Chrome) **cap the number of TCP connections per domain** to avoid abuse (commonly **6 connections per domain**).
- So if you open **6 SSE connections**, all 6 are tied up forever → no free connection for CSS, JS, images, or API calls → **starvation**.
---

### Why this happens

1. **HTTP/1.0** → one request per connection, no streaming at all (must close after response).
    
2. **HTTP/1.1** → added "keep-alive" connections (reuse connections), but:
    
    - Still **only 1 request [[in-flight]] per connection** (no multiplexing).
        
    - Pipelining (queueing multiple requests on same connection) exists but is unreliable, so browsers avoid it.
        
3. SSE keeps the connection **open forever** → blocking.
    

That’s why browsers allow up to ~6 parallel connections per domain → to let you download multiple files at once (CSS, JS, images).  
But if all of them are SSE streams, you choke the browser.

---

### HTTP/2 Fix

- **HTTP/2** multiplexes many streams inside a single connection.
    
- One connection to the server can serve:
    
    - Your **CSS, JS, images**
        
    - Multiple **SSE streams**
        
    - Other API requests  
        all **without blocking each other**.
        
- So the browser’s "6 connection limit" isn’t a problem anymore.  
    HTTP/2 is preferred for SSE-heavy apps.
    

---

✅ **In short:**

- **HTTP/1.1 problem:** one request per connection, browsers cap per-domain connections (≈6). SSE hogs connections → blocks other requests.
    
- **HTTP/2 solution:** multiplexing allows many streams over one connection, removing this limitation.
    

---


#### Example
1. Clients makes a simple request, the server responds with a special header:
```js
10 app.get("/stream", (req, res) => {
11 res.setHeader("Content-Type", "text/event-stream");
12 send(res);
13 });
14 function send(res) {
15   res.write("data: Some Data\n\n"); // based on some logic
16 }
```
and anything that is sent after that (line 11) will be interpreted by the client as different streams, as long as you start with the word "data: " and end with two new lines "\n\n"
this parsing is done by the browser, this is the client code:
```js
let sse = new EventSource("http://localhost:8080/stream");
sse.onmessage = console.log
```
this Event source object is shipped with every browser