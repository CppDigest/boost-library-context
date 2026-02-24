# #369 - async_receive2: race conditions on incomplete messages [Closed]

> Username: anarthal  
> Created at: 2025-12-03 10:27:16 UTC  
> Updated at: 2026-01-07 08:55:24 UTC  
> Closed at: 2026-01-07 08:55:24 UTC  
> Url: https://github.com/boostorg/redis/issues/369  

(Translating here a Slack conversation)  
  
There is a potential race condition with async_receive2 as it is now:  
  
* The server sends many pushes in a packet. Let's say 10, but the last one (the 10th) is incomplete. Some nodes for the 10th push are in the receive response, and some others aren't.  
* Since there is at least one complete push, async_receive2 completes.  
* User code ends up calling .clear()  
* The final nodes for the 10th push are received. You now have an incomplete message delivered to the user  
  
This is likely an indication that a plain `generic_flat_response` is too low-level to be used directly in PubSub.
