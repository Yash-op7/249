Head-of-Line (HOL) blocking in TCP ==occurs when a lost packet forces all subsequent, in-order packets in the same TCP connection to wait in the receiver's buffer until the lost packet is retransmitted and received==. Because TCP guarantees ordered delivery, a single packet loss can significantly delay the delivery of independent data, even if that subsequent data has already arrived at the receiver. This issue is inherent to the TCP protocol and was a significant performance bottleneck for earlier web protocols like HTTP/1.1, with modern protocols like HTTP/3 built on the [QUIC protocol](https://www.google.com/search?sca_esv=409625519d2394b4&q=QUIC+protocol&sa=X&ved=2ahUKEwjvwtHoloOQAxWAyzgGHZoYBUcQxccNegQIJxAB&mstk=AUtExfBjTNeU5e4368fTzH9r8bg1AtyL4fFnTpSZLCuDmgDTEQwganMnW4vtO67loRbDyqfAu4d1SaW8H385l-b3E5R84lDPcipBuC6H6CJrhV91fM6rx_4W6K5LAuoaCGaaxUE&csui=3) addressing it by introducing streams and independent packet ordering.  

How TCP HOL Blocking Works

- **Ordered Delivery Requirement:** 
    
    ⭐ TCP requires all data to be delivered to the receiving application in the exact order it was sent. 
    

- **Packet Loss:** 
    
    A packet is lost during transmission, and it gets retransmitted. 
    

- **Subsequent Packets are Delayed:** 
    
    Any packets that arrive at the receiver after the lost packet but are intended to be delivered before it are held in the TCP buffer. 
    

- **Dependency:** 
    
    These subsequent packets cannot be processed and delivered to the application until the missing "head" packet is successfully retransmitted and arrives. 
    

Impact on Web Browsing

- **Single TCP Connection:** 
    
    A browser opening a web page may receive different pieces of data (e.g., images, CSS, JavaScript) over the same TCP connection, and some of these might be semantically independent. 
    

- **Dependency Chain:** 
    
    If a small packet containing a piece of one image is lost, all subsequent data, including entire other images and scripts, will be blocked until that lost piece is resent. 
    

- **Performance Penalty:** 
    
    This causes a delay in rendering the web page, even if the majority of the data has already arrived. 
    

Solutions

- **[HTTP/2](https://www.google.com/search?sca_esv=409625519d2394b4&q=HTTP%2F2&sa=X&ved=2ahUKEwjvwtHoloOQAxWAyzgGHZoYBUcQxccNegUIiQIQAQ&mstk=AUtExfBjTNeU5e4368fTzH9r8bg1AtyL4fFnTpSZLCuDmgDTEQwganMnW4vtO67loRbDyqfAu4d1SaW8H385l-b3E5R84lDPcipBuC6H6CJrhV91fM6rx_4W6K5LAuoaCGaaxUE&csui=3):** 
    
    ⭐ Introduced multiplexing, allowing multiple requests and responses over a single TCP connection, but it still suffered from TCP-level HOL blocking. 
    

- **[QUIC Protocol](https://www.google.com/search?sca_esv=409625519d2394b4&q=QUIC+Protocol&sa=X&ved=2ahUKEwjvwtHoloOQAxWAyzgGHZoYBUcQxccNegUIiAIQAQ&mstk=AUtExfBjTNeU5e4368fTzH9r8bg1AtyL4fFnTpSZLCuDmgDTEQwganMnW4vtO67loRbDyqfAu4d1SaW8H385l-b3E5R84lDPcipBuC6H6CJrhV91fM6rx_4W6K5LAuoaCGaaxUE&csui=3) (HTTP/3):** 
    
    Aims to solve this by operating over UDP and implementing multiple independent streams, effectively removing HOL blocking at the transport layer and allowing lost packets on one stream to not affect others.