# #175 - File descriptor passing over unix domain socket? [Closed]

> Username: hoditohod  
> Created at: 2018-12-05 12:59:28 UTC  
> Updated at: 2020-12-30 00:57:55 UTC  
> Closed at: 2020-12-30 00:57:54 UTC  
> Url: https://github.com/boostorg/asio/issues/175  

AF_UNIX sockets (usually) have the ability to pass an open file descriptor over the socket ([example](https://stackoverflow.com/questions/2358684/can-i-share-a-file-descriptor-to-another-process-on-linux-or-are-they-local-to-t)), does ASIO support this?  
  
I have an async unix domain socket *server*, and I'd like to *send* open fd's to unprivileged clients. Is it possible with asio (directly or any other way)? The AISO model makes the server so simple, I wouldn't want to change it...

---

## Comment 1

> Username: djarek  
> Created at: 2018-12-06 02:49:34 UTC  
> Url: https://github.com/boostorg/asio/issues/175#issuecomment-444729451  

Here's an example that shows how to send an fd after a fork from parent to child: https://gist.github.com/djarek/aeebfd739be1e375d6b626d6011afc9c  
  
It should be possible to implement a regular proactor-style read/write operation using this. Note that in real code you'll need to handle EWOULDBLOCK from the send/receive functions.  
I'm not quite sure why `wait_error` doesn't work with `async_wait`, but I might be doing something wrong on POSIX API side :).

---

## Comment 2

> Username: hoditohod  
> Created at: 2018-12-06 11:10:35 UTC  
> Url: https://github.com/boostorg/asio/issues/175#issuecomment-444836302  

Hi Damian!  
Thanks for the reply! (1st I didn't even notice, as my mailbox is loaded with your activity on Beast :wink:)  
  
As I understand you simply got the fd with native_handle() and used the synchronous POSIX API's. BTW, nice and complete example!  
  
My server uses the proactor pattern, and pushes the buffer to the client with an asio::async_write call(), so I'm particularly interested in sending the buffer+fd pair with some asio::async_* method.

---

## Comment 3

> Username: djarek  
> Created at: 2018-12-06 20:23:36 UTC  
> Updated at: 2018-12-06 20:23:45 UTC  
> Url: https://github.com/boostorg/asio/issues/175#issuecomment-445016920  

I might be wrong, but ASIO doesn't support non-inline out-of-band data, because the underlying POSIX sockets don't support them:  
>UNIX domain sockets do not support the transmission of out-of-band  
       data (the MSG_OOB flag for send(2) and recv(2)).  
  
I don't think you'll be able to avoid touching the native APIs. It's up to you if you'll put the work required to make it compatible with the ASIO model (it's non-trivial, but possible).

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 00:57:53 UTC  
> Url: https://github.com/boostorg/asio/issues/175#issuecomment-752290765  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#658](https://github.com/chriskohlhoff/asio/issues/658).
