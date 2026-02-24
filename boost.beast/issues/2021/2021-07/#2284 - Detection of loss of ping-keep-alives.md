# #2284 - Detection of loss of ping/keep-alives [Closed]

> Username: treyweaver  
> Created at: 2021-07-13 13:12:23 UTC  
> Updated at: 2021-08-03 10:01:16 UTC  
> Closed at: 2021-08-03 10:01:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2284  

I am using version 1.76 on an ARM Cortex processor.  I am running as a client  
  
I have a wss socket established and with wireshark I can see that websocket's ping/keep-alives are working.  Then I disconnect the server from the network to stop the pings.  
  
On the client side I do not see any indication that the link has been lost.  I expected to get an on_read with an error code.  
  
How can I tell if the websocket's ping/keep-alives have been lost?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-07-13 13:48:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2284#issuecomment-879104563  

By default the client does not initiate pings. The server does. You can enable pings from the client by setting the option on the websocket stream prior to the handshake

---

## Comment 2

> Username: treyweaver  
> Created at: 2021-07-13 14:10:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2284#issuecomment-879122719  

That was not my question.  The server is delivering pings and every thing is running fine.  But for some reason those pings disappear; maybe a cable get cut, a router goes down, someone unplugs a cable.  How does the user of beast know that the server is no longer connected?  My client connection to the server is persistent, so I have to know the connection is lost so I can try to reconnect.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-07-13 21:54:24 UTC  
> Updated at: 2021-07-13 21:54:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2284#issuecomment-879428211  

Your client cannot know that the connection has been cut unless it initiates pings.  
  
the server initiated pings tell the server that the connection has been cut.  
  
the lack of pong response to a ping will result in your async_read completing with an error.

---

## Comment 4

> Username: treyweaver  
> Created at: 2021-07-14 12:12:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2284#issuecomment-879839026  

OK that fixed the issue.  I noticed that the client default for keep_alive_pings is "false".  Can I always assume that the websocket server will respond to client pings?  
  
Thanks!

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-07-14 12:27:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2284#issuecomment-879848113  

That is my understanding, yes. Our thinking here is that servers care whether there are zombie clients taking up resources and want to cull them often. Clients tend not to need to do this (although there are exceptons such as yours)
