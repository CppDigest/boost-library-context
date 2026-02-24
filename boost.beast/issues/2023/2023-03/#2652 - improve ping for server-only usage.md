# #2652 - improve ping for server-only usage [Closed]

> Username: johannesparty  
> Created at: 2023-03-15 18:30:45 UTC  
> Updated at: 2024-01-01 09:10:14 UTC  
> Closed at: 2024-01-01 09:10:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2652  

### Version of Beast  
  
All versions  
  
### Description of the problem   
  
The current ping implementation lets you set a "inactivity timeout" which is triggered when no data is received in a set amount of time.  You can also set a "send ping" flag, which will send a ping after 1/2 the timeout has occurred.  
  
It is common that only the "server" side of a connection sends ping as Browser WebSockets typically don't have this ability.  Unfortunately, the current implementation doesn't adequately handle this case.  Here is an example:  
  
Server = beast, with timeout and ping turned on.  
Client = browser, can't send ping  
  
Client sends message to server more often than the inactivity timeout.  
Server doesn't send any pings as a result  
   
Problem: Client never gets any messages or pings, so when the client receives no pings or messages in a long time, it assumes the connections is closed due to inactivity.  
  
### Proposal  
  
When "send ping" is turned on, send the ping if you haven't received anything within 1/2 timeout OR you haven't sent anything within the 1/2 timeout.  I sample code for ping on nodejs do it this way.  
  
If you don't want to add the additional pings (which should be harmless), then a new separate flag could be added to enable this behavior.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-03-16 03:28:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2652#issuecomment-1471242025  

I don't follow. You're supposed to set the send_ping on the server side and nothing on the client side. Where's the problem?

---

## Comment 2

> Username: johannesparty  
> Created at: 2023-03-16 16:20:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2652#issuecomment-1472290675  

I want the client side to know when data is no longer getting through.  Typically this is done with the client side doing a timeout (no data received in <duration>).  This just requires the server to send a ping if it hasn't sent any data in <duration>/2.  "Any data" could mean a regular message or a ping message.    
  
The client side never sends a ping, it really just passively expects server ping (or other traffic) within <duration> timeout.  
  
This should only involve pretty minor changes to existing code; currently ping is sent if enabled and nothing received in last <duration>/2.  Make it so ping is sent if enabled and nothing received OR SENT in last <duration>/2.  I don't think the API needs to change, but I would be ok if this behavior requires a separate setting.  
  
Here is a typical sample from javascript world (kind of relevant; these are WEBsocket after all).  https://github.com/websockets/ws#how-to-detect-and-close-broken-connections  
In particular, see the "You might want to add a ping listener..." section.  
  
Note their code sends pings every 30s regardless of other traffic and relies on ping exclusively to determine timeout.   We can optimize by viewing any received traffic resetting the timeout.  
  
Thanks!

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-03-17 00:30:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2652#issuecomment-1472937556  

You can do that now by just calling async_ping to manually check. (NOTE: I should write an example for that).  
  
What do you want to happen if there's no activity?

---

## Comment 4

> Username: johannesparty  
> Created at: 2023-03-17 16:42:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2652#issuecomment-1474112206  

Yes, I could handle all the timeout stuff myself, but the current implementation of set_option's timeout does 80% of what I need, so IMO beast should either send a ping every 1/2 timeout if no other data has been sent and ping is turned on (at least as an option).  I wouldn't ask for this if I thought this was an unusual usage of ping or if I thought this was a requirement other users won't share.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-03-19 12:45:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2652#issuecomment-1475243261  

Fair point. Is there a convention or standard for this, or is this your customsolution?

---

## Comment 6

> Username: ashtum  
> Created at: 2024-01-01 09:10:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2652#issuecomment-1873237745  

Duplicate of https://github.com/boostorg/beast/issues/2716
