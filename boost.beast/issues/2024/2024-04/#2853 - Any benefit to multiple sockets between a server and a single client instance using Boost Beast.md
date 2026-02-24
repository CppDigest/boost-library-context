# #2853 - Any benefit to multiple sockets between a server and a single client instance using Boost Beast? [Closed]

> Username: ryanlandvater  
> Created at: 2024-04-13 15:39:22 UTC  
> Updated at: 2024-04-14 21:48:08 UTC  
> Closed at: 2024-04-14 21:48:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2853  

From what I have read it seems to me that because IO calls to the kernel cannot be execute in a parallel manner, there’s really no benefit to opening multiple separate sockets on different ports between a client and server instance.  
  
For context, working on an application that transmits huge amount of image data between a server instance and a client application for diagnostic image viewing (I use beast for its awesome performance). Because the amount of data is so immense, I’ve been trying to really optimize the amount of data I can push through.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-04-13 15:42:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2853#issuecomment-2053683054  

The first thing to try is to do as much work as possible per I/O. That means, you want to transfer large buffers of data. Say, at least one megabyte per I/O call. If you have very many small buffers you should try to combine them into a single BufferSequence, or try copying them into a single, large destination buffer. You will need to measure these approaches.  
  
Are you using HTTP, or Websocket?

---

## Comment 2

> Username: ryanlandvater  
> Created at: 2024-04-13 16:34:03 UTC  
> Updated at: 2024-04-13 16:36:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2853#issuecomment-2053695667  

Awesome thank you! And just to help others reading this from our separate email thread, **there is no benefit from opening multiple separate sockets on different ports between a client and server instance**. It won't improve raw throughput.   
  
I am using websocket as I think the upgraded protocol and ability to push data as it becomes available to the server from disk is most beneficial for this implementation.    
  
If there is packet drop, does that delay the transmission to other clients or does the packet failure and TCP resend get a new spot in the the io_context run queue? As there will be many clients expecting imaging data from each containerized instance, ensuring all do not slow down due to one client's poor connection was another motivation for multiple sockets per client design.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-04-14 18:01:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2853#issuecomment-2054135931  

> If there is packet drop, does that delay the transmission to other clients or does the packet failure and TCP resend get a new spot in the the io_context run queue? As there will be many clients expecting imaging data from each containerized instance, ensuring all do not slow down due to one client's poor connection was another motivation for multiple sockets per client design.  
  
TCP packet retransmission (and other low-level tasks like sequence checking) is handled by the operating system's network stack, and they don't directly impact the application.

---

## Comment 4

> Username: ryanlandvater  
> Created at: 2024-04-14 21:48:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2853#issuecomment-2054193932  

Thank you
