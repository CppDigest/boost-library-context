# #2987 - Support no-masking extension [Open]

> Username: xim  
> Created at: 2025-02-20 13:53:54 UTC  
> Updated at: 2025-08-24 17:40:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2987  

I am working with software that has a number of websocket clients ; some on the local machine over local sockets, some over SSL tunnels, some over TCP, etc.  
  
For the clients running in a secure context, I wouldn't mind saving a few CPU cycles by skipping the standard websocket masking. What I want is perfectly covered by a draft RFC, and I was wondering if this would be interesting to Boost.Beast.  
  
I could try to make a PR at some point, but won't bother if there's no interest in the feature from core developers. =)  
  
https://datatracker.ietf.org/doc/draft-damjanovic-websockets-nomasking/

---

## Comment 1

> Username: ashtum  
> Created at: 2025-02-20 15:27:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2987#issuecomment-2671839217  

Thanks for the suggestion. Clients can also use [websocket::stream::secure_prng](https://www.boost.org/doc/libs/1_87_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/secure_prng.html) to disable the secure PRNG. Performance measurements are needed to determine how much gain is achievable by completely disabling masking and whether it is worthwhile.  
  
Additionally, the referenced RFC is still a draft, so introducing a feature that breaks the current standard may not be advisable.

---

## Comment 2

> Username: xim  
> Created at: 2025-02-26 11:56:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2987#issuecomment-2684751620  

I did some benchmarking to compare boost::beast websocket performance inside our application framework. There is some overhead in the framework itself, so this isn't a raw comparison of boost::beast performance.  
  
I ran the tests in three configurations, doing the benchmark 20 times and selecting the best.  
  
1. non-patched beast, secure PRNG  
2. non-patched beast, non-secure PRNG  
3. patched beast that doesn't mask  
  
Timings came out (server and client on the same low-end embedded platform, transferring 100 MB of messages)  
  
1. 2776ms  
2. 2771ms  
3. 2161ms  
  
I did some less-rigorous testing on x86, and there all three appear to fall within 3% of each other. Surprisingly, on x86 the PRNG change giving sligthly more gain than the no-masking.  
  
So it appears that a no-masking extension could make sense in scenarios where you are CPU constrained.

---

## Comment 3

> Username: xim  
> Created at: 2025-02-26 12:10:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2987#issuecomment-2684780521  

Re  
  
> introducing a feature that breaks the current standard may not be advisable  
  
Yeah, when the extension is negotiated, I guess we'd be ignoring some MUSTs in the current rfc. It would have to be opt-in on both server and client, at least...

---

## Comment 4

> Username: ashtum  
> Created at: 2025-03-08 16:28:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2987#issuecomment-2708380816  

> Timings came out (server and client on the same low-end embedded platform, transferring 100 MB of messages)  
>   
> 1. 2776ms  
> 2. 2771ms  
> 3. 2161ms  
>   
> I did some less-rigorous testing on x86, and there all three appear to fall within 3% of each other. Surprisingly, on x86 the PRNG change giving sligthly more gain than the no-masking.  
  
What was the size of the messages in the test? The PRNG is only used to create a 4-byte key, which is then used to mask the entire frame. Therefore, for large messages, it is expected that the masking operation will take the majority of the time.  
  
> Yeah, when the extension is negotiated, I guess we'd be ignoring some MUSTs in the current rfc. It would have to be opt-in on both server and client, at least...  
  
Yes, specifically:  
  
> [5.1](https://www.rfc-editor.org/rfc/rfc6455.html#section-5.1).  Overview  
   ... a client MUST mask all frames that it  
   sends to the server (see [Section 5.3](https://www.rfc-editor.org/rfc/rfc6455.html#section-5.3) for further details).  (Note  
   that masking is done whether or not the WebSocket Protocol is running  
   over TLS.)  The server MUST close the connection upon receiving a  
   frame that is not masked.  In this case, a server MAY send a Close  
   frame with a status code of 1002 (protocol error) as defined in  
   [Section 7.4.1](https://www.rfc-editor.org/rfc/rfc6455.html#section-7.4.1).  A server MUST NOT mask any frames that it sends to  
   the client.  A client MUST close a connection if it detects a masked  
   frame.  In this case, it MAY use the status code 1002 (protocol  
   error) as defined in [Section 7.4.1](https://www.rfc-editor.org/rfc/rfc6455.html#section-7.4.1).  (These rules might be relaxed in  
   a future specification.)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2025-08-24 17:40:16 UTC  
> Updated at: 2025-08-24 17:40:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2987#issuecomment-3218260357  

The cost of the PRNG only matters for a client sending a large number of small messages. Servers do not mask outgoing messages.
