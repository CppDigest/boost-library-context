# #226 - allow per-message compression option [Closed]

> Username: vinniefalco  
> Created at: 2017-01-18 12:54:33 UTC  
> Updated at: 2024-06-06 05:34:18 UTC  
> Closed at: 2022-10-03 01:36:40 UTC  
> Url: https://github.com/boostorg/beast/issues/226  

When permessage-deflate is negotiated on a stream, It should be possible to control whether compression is enabled on a per-message basis.

---

## Comment 1

> Username: JunielKatarn  
> Created at: 2018-09-05 23:36:27 UTC  
> Updated at: 2018-09-05 23:36:40 UTC  
> Url: https://github.com/boostorg/beast/issues/226#issuecomment-418915092  

Has this been implemented yet (as of boost 1.68)?  
If not, when is message compression used in a Beast WebSocket client?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-09-06 00:23:14 UTC  
> Url: https://github.com/boostorg/beast/issues/226#issuecomment-418923027  

There are three things which need happen for compression to be active on a stream:  
  
1. Compile-time enablement. This is achieved through a `bool deflateSupported` template parameter, which defaults to true. Setting it to false can make your compiled code smaller.  
  
2. Run-time enablement. This is controlled by the function `stream::set_option`. By default, clients are enabled and servers are disabled. However, this is only meaningful if `deflateSupported==true`. Otherwise, if compile-time enablement is off then permessage-deflate (the canonical term for websocket compression) is of course never possible.  
  
3. Negotiation enablement. When the WebSocket upgrade request/response cycle is performed, the client and server attempt to negotiate (or not) the permessage-deflate settings. The outcome of this negotiation depends on both the compile-time and the run-time enablement of both peers. The client can request compression, but the server has to OK it. Otherwise there is no compression. The `permessage_deflate` option structure allows the client to configure the permissible results of the handshake.  
  
This complexity is unfortunately a consequence with the permessage-deflate specification, which you might try reading:  
https://tools.ietf.org/html/rfc7692  
  
Hope this helps!

---

## Comment 3

> Username: JunielKatarn  
> Created at: 2018-09-06 00:57:46 UTC  
> Url: https://github.com/boostorg/beast/issues/226#issuecomment-418928483  

It certainly does. If I got this correctly, by default, a Beast WS client stream has compression (per-message deflate) enabled, pending other conditions (server negotiation).  
  
Thanks.

---

## Comment 4

> Username: pscamodio  
> Created at: 2020-11-19 08:11:23 UTC  
> Url: https://github.com/boostorg/beast/issues/226#issuecomment-730204269  

Sorry to resurrect this old issue, but it's still open.  
After the three conditions are met the server and client "can" exchange compressed messages. But it seems to be no way to specify, for a specific message if I want to compress it or not.  
It seems to me that beast suppose that if permessage_deflate is enabled I want to compress all the messages.  
In my case there are some messages that I need to leave uncompressed for speed reason, the time spent in deflate is not acceptable, but I want to compress all the other messages.  
I don't see in the write methods any way to specify if that specific message need to be compressed or not.

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-07-05 18:14:12 UTC  
> Url: https://github.com/boostorg/beast/issues/226#issuecomment-1175352948  

@pscamodio   
  
From the height of my ignorance, doesn't this work?  
  
```cpp  
// Disable compression  
websocket::permessage_deflate pmd;  
ws.get_options(pmd);  
pmd.client_enable = false;  
pmd.server_enable = false;  
ws.set_options(pmd);  
// Use the was stream  
ws.read(/* ... */); // or whatever operation  
// Enable compression  
ws.get_options(pmd);  
pmd.client_enable = true;  
pmd.server_enable = true;  
ws.set_options(pmd);  
```  
  
If this does work, then it might not be a huge problem, although this is probably verbose enough to justify some wrapper function.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-07-05 18:40:13 UTC  
> Updated at: 2022-07-05 18:40:57 UTC  
> Url: https://github.com/boostorg/beast/issues/226#issuecomment-1175374614  

I like your thinking here, but the meaning of "enable" is just that it is allowed as a negotiation option during handshake. Whether or not compression is actually enabled is a property of the stream. See:  
https://github.com/boostorg/beast/blob/00293a6adb5383fe14217a8916dd7ff79a857483/include/boost/beast/websocket/impl/stream_impl.hpp#L100  
  
The code to implement this might already be there, you would just need to add a member function and tests.

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-07-05 18:44:37 UTC  
> Url: https://github.com/boostorg/beast/issues/226#issuecomment-1175378018  

Yes. I was looking at   
  
https://github.com/boostorg/beast/blob/00293a6adb5383fe14217a8916dd7ff79a857483/include/boost/beast/websocket/impl/stream_impl.hpp#L227-L228  
  
right now, and trying to find a solution to this issue and https://github.com/boostorg/beast/issues/227.  
  
So this issue is just a matter of agreeing on the API to enable/disable `wr_compress_opt`, while #227 is just a matter of including an extra min size condition at the line above.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-07-05 19:01:28 UTC  
> Url: https://github.com/boostorg/beast/issues/226#issuecomment-1175400263  

The minimum size should be configurable, with a reasonable default (see what other libs do, like uWebsockets)
