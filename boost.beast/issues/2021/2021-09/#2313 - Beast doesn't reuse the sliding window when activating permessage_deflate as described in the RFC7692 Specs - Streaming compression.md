# #2313 - Beast doesn't reuse the sliding window when activating permessage_deflate as described in the RFC7692 Specs / Streaming compression [Closed]

> Username: chreniuc  
> Created at: 2021-09-16 13:50:08 UTC  
> Updated at: 2021-11-03 21:42:43 UTC  
> Closed at: 2021-11-03 21:42:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2313  

We activated `permessage_deflate` and we've noticed that the beast library doesn't behave as the RFC7692 specs say [here](https://datatracker.ietf.org/doc/html/rfc7692#section-7.2.3.2), which means that it doesn't reuse the sliding window of the [LZ77](https://en.wikipedia.org/wiki/LZ77_and_LZ78#LZ77) algorithm.  
  
**Summary of the specs:**  
  
If a client wants to send two compressed messages with the same contents(`Hello`), and the client did not receive `client_no_context_takeover` from the server. The same thing applies to the server, when it doesn't receive `server_no_context_takeover`. If the server sends that message twice, the first one will have the following payload:  
  
```  
0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00  
```  
  
And the second message will have the following payload:  
  
```  
0xf2 0x00 0x11 0x00 0x00  
```  
  
So 2 bytes are saved.  
  
**How beast does it:**  
  
I've started from the async [client](https://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/example/websocket/client/async/websocket_client_async.cpp) and [sever](https://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/example/websocket/server/async/websocket_server_async.cpp) examples, with a few minor additions, the main one was activating `permessage_deflate`, both in client and server. I've created [this mini](https://github.com/chreniuc/beast_permessage_deflate_sliding_window) repository that contains the code to reproduce this problem(?).  
  
The most important addition to the examples is [this](https://github.com/chreniuc/beast_permessage_deflate_sliding_window/commit/6045604b4c787bca976a25475e426b9088954147#diff-37416bd90f6b7e851b4e0c946f5f40c2b35a74e75f14268a0184e2122bfdfd84L125), which activates `permessage_deflate`:  
  
```c++  
boost::beast::websocket::permessage_deflate opt;  
opt.client_enable = true; // for clients  
opt.server_enable = true; // for servers  
opt.client_no_context_takeover = false;  
opt.server_no_context_takeover = false;  
ws_.set_option(opt);  
```  
  
  
After compiling the client and server, we've noticed that this doesn't work as the specs say. We've noticed that if we send that message multiple times, we send the same payload(`0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00`), which means that the beast library doesn't use that sliding window from the previous messages, it compresses the messages starting with a new sliding window for each message. This is allowed by the specs, as it is stated [here](https://datatracker.ietf.org/doc/html/rfc7692#section-7.2.1):  
  
>If the "agreed parameters" contain the "server_no_context_takeover"  
extension parameter, the server MUST start compressing each new  
message with an empty LZ77 sliding window.  **Otherwise, the server MAY  
take over the LZ77 sliding window used to build the last compressed message.**  
  
The word **MAY** allows both: using a new sliding window or the one used for the other messages.  
  
The client handshake request was this(which doesn't contain `server_no_context_takeover` or `client_no_context_takeover`):  
```  
Hypertext Transfer Protocol  
    GET / HTTP/1.1\r\n  
    Host: localhost:8080\r\n  
    Upgrade: websocket\r\n  
    Connection: upgrade\r\n  
    Sec-WebSocket-Key: pZUiXL2tqkB3g8xDeogeJw==\r\n  
    Sec-WebSocket-Version: 13\r\n  
    Sec-WebSocket-Extensions: permessage-deflate; server_max_window_bits=15; client_max_window_bits=15\r\n  
    User-Agent: Boost.Beast/266 websocket-client-async\r\n  
    \r\n  
    [Full request URI: http://localhost:8080/]  
    [HTTP request 1/1]  
    [Response in frame: 6]  
```  
  
The server handshake request was this(which also doesn't contain `server_no_context_takeover` or `client_no_context_takeover`):  
  
```  
Hypertext Transfer Protocol  
    HTTP/1.1 101 Switching Protocols\r\n  
    Upgrade: websocket\r\n  
    Connection: upgrade\r\n  
    Sec-WebSocket-Accept: na/r0xaj84eZ/KI2FQ+lu02dwZ8=\r\n  
    Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits=15\r\n  
    Server: Boost.Beast/266 websocket-server-async\r\n  
    \r\n  
    [HTTP response 1/1]  
    [Time since request: 0.000771627 seconds]  
    [Request in frame: 4]  
    [Request URI: http://localhost:8080/]  
```  
  
The first `Hello` send by the server has the following payload `f2 48 cd c9 c9 07 00` and the second, has the same payload. When it should've had `f2 00 11 00 00`.  
  
I also attached the complete capture, can be visualized with wireshark: [capture_permessage_deflate_1.zip](https://github.com/boostorg/beast/files/7178211/capture_permessage_deflate_1.zip)  
  
  
We are using the following:  
  
 - Beast version - `266`  
 - Boost version - `1.71.0`  
 - Compiler - `gcc 9.2.0`  
  
**Why is this important for us?**  
  
After some investigations in our server, we noticed that most of the payloads of our messages(70-80%, we're sending up to 100k messages per second in total, to multiple ws connections) are small(from 23 bytes up to 60 bytes), and if we activate compression, the size of the compressed payload gets bigger than the size of uncompressed(if we send `Hello`, if uncompressed we will send 5 bytes, and if we compress it we will send 7 bytes). We also did a test with `gzip`, we've compressed a file locally that had around 33 bytes and the resulted compressed size, was almost double(64 bytes). So we thought that the same thing might happen when we have `permessage_deflate` activated(because most of our messages are around that size). And there is also the cpu used to do that compression, so there are only disadvantages if we activate that extension for small messages.  
  
That's why we wanted to see if WebSocket protocol supports streaming compresion, and after reading the specs, based on what it says there we got to the conclusion that it supports, if it reuses the sliding window from the previous messages as it stated [here in the specs](https://datatracker.ietf.org/doc/html/rfc7692#section-7.2.3.2).  
  
**Questions:**  
  
 * Do you guys have any idea on it doesn't reuse the sliding window? Is this not supported by beast or are we doing something wrong? If atm is not supported by beast, is there a plan on supporting it in the future?  
 * Is there any other way(that works) on using streaming compression when compressing the messages?  
 * I've noticed that when we compress with `gzip` a file that contains the following string `Lorem ipsum dolor sit amet, conse`(33 bytes), we end up with a compressed file that has `65 bytes`. But when we send that text via websocket, which compresses it, the syze of the compressed payload is `35 bytes`. Both are using the [DEFLATE](https://en.wikipedia.org/wiki/Deflate)(or [rfc1951 specs](https://datatracker.ietf.org/doc/html/rfc1951)) algorithm for compressing(gzip and premessage_deflate, as stated in the specs), so why are these differences in sizes after compression?  
   
 Thank you for taking the time to read this.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-09-16 14:03:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2313#issuecomment-920931686  

Yes it should work, the code is there and it looks at the settings:  
https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/detail/impl_base.hpp#L155  
  
`zo.reset()` resets the sliding window. It should only be called if `this->pmd_config_.server_no_context_takeover` is true. Your code sets it to false. So there must be another piece of information that we are missing. Perhaps you can set breakpoints or logging in this function to help diagnose it? Thanks

---

## Comment 2

> Username: chreniuc  
> Created at: 2021-09-16 15:43:21 UTC  
> Updated at: 2021-09-16 15:43:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2313#issuecomment-921016918  

I've tried to investigate this a little, until now I've noticed the following:  
  
There are two reset methods on that `zo`, one with params and one without, there are only two calls to that method after we call `async_accept`.  
  
```  
async_accept                                                                                                                                                                                     
do_pmd_config                                                                                                                                                                                    
open_pmd                                                                                                                                                                                         
doReset(params)                                                                                                                                                                                  
doReset(params)                                                                                                                                                                                  
on_accept  
```  
  
When we call async write, these methods are called(from [this file](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/detail/impl_base.hpp)) and context is set to false, as it should, also, no reset calls:  
  
```  
async_write  
pmd_enabled  
pmd_enabled  
deflate  
do_context_takeover_write: Server. no_context: 0  
```  
  
I will continue investigating tomorrow.

---

## Comment 3

> Username: chreniuc  
> Created at: 2021-09-23 13:35:33 UTC  
> Updated at: 2021-09-23 15:06:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2313#issuecomment-925822751  

I've continued investigating and managed to find a working fix for this.  
  
**The fix is to use `zlib::Flush::sync` instead of `zlib::Flush::full`, [here](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/websocket/detail/impl_base.hpp#L140)**  
  
I did a pull request: https://github.com/boostorg/beast/pull/2318  
___  
  
Details:  
  
After some time investigating, I got to the conclusion that the error must be in the way the zlib is used inside the beast library. But to be sure that what the [specs](https://datatracker.ietf.org/doc/html/rfc7692#section-7.2.3.2) say, works, first, I wanted to have a working example using the official zlib library. So I came up with this code which uses zlib and does exactly what [the specs](https://datatracker.ietf.org/doc/html/rfc7692#section-7.2.3.2) say:  
  
```c++  
  string to_be_compressed{"Hello"};  
  char out[100] = {0};  
  z_stream strm; int level = 8; int flush;  
  strm.zalloc = Z_NULL; strm.zfree = Z_NULL; strm.opaque = Z_NULL; strm.avail_in = 0;  
  int ret = deflateInit2(&strm, level, Z_DEFLATED, -15, 8, Z_DEFAULT_STRATEGY); // negative for window, to get raw  
  
  // first deflate  
  strm.avail_in = to_be_compressed.size(); // updated by deflate  
  strm.next_in = reinterpret_cast<const unsigned char*>(to_be_compressed.data()); // updated by deflate  
  strm.avail_out = 100;  
  strm.next_out = reinterpret_cast<unsigned char*>(out);  
  // flush = Z_FULL_FLUSH; -> Does exactly what beast currently does.  
  flush = Z_SYNC_FLUSH;  
  ret = deflate(&strm, flush);  
  
  // Second deflate  
  strm.avail_in = to_be_compressed.size();   
  strm.next_in = reinterpret_cast<const unsigned char*>(to_be_compressed.data());   
  strm.avail_out = 100;  
  strm.next_out = reinterpret_cast<unsigned char*>(out);  
  flush = Z_SYNC_FLUSH;  
  ret = deflate(&strm, flush);  
  
  (void) deflateEnd(&strm);  
```  
  
The code from above compresses `Hello` twice. I did two tests:  
 * I've tested with the `flush` set to `Z_FULL_FLUSH` for both compressions, the result was just like beast does it atm, I've received `0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00` after both compressions.  
 * Next, I've set the `flush` to `Z_SYNC_FLUSH`, and I've noticed that for the first compression we receive: `0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00` and for the second we receive: `0xf2 0x00 0x11 0x00 0x00`, just like the specs say.  
  
After being sure that this is something that can be accomplished via zlib library, I've started checking the code from beast.  
  
I've checked the code from the [deflate](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/websocket/detail/impl_base.hpp#L83) method from beast, and as you can see it's similar to the code from above(The example using zlib).  
  
First I've noticed that `Flush::sync` isn't used anywhere in this code(`Z_SYNC_FLUSH`), I've just noticed [zlib::Flush::none](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/websocket/detail/impl_base.hpp#L103), [zlib::Flush::block](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/websocket/detail/impl_base.hpp#L132) and [zlib::Flush::full](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/websocket/detail/impl_base.hpp#L140).  
  
So I had no idea which one should be changed, so I tried to debug and see which lines are executed from that method. And I've noticed that all 3 `write` calls are done(with the `flush`es from above), when the deflate method is called with the `Hello` string.  
  
Then, I wanted to see exactly what does [write](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/zlib/detail/deflate_stream.ipp#L360) do in each case, and the most interesting thing I've found was that when the `flush` was set to `zlib::Flush::full`, [here](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/zlib/detail/deflate_stream.ipp#L472), it called [clear_hash()](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/zlib/detail/deflate_stream.hpp#L510).  
  
So, instead of using `zlib::Flush::full`, in the [deflate](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/websocket/detail/impl_base.hpp#L140) method, I've used `zlib::Flush::sync`.  
  
I did a test, and it worked as we expected(as the [rfc7692 says](https://datatracker.ietf.org/doc/html/rfc7692#section-7.2.3.2)):  
  
 * First `Hello` was compressed like this: `0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00`  
 * The second `Hello` was compressed like this: `0xf2 0x00 0x11 0x00 0x00`  
  
I also tested when the client and the server do send `client_no_context_takeover` and `server_no_context_takeover`:  
  
 * First `Hello` was compressed like this: `0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00`  
 * The second `Hello` was compressed like this: `0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00`  
  
Which is correct, because when one of those parameters is send in the handshake, the other end must use an empty sliding window(as the specs say). And this works because [do_context_takeover_write](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/websocket/detail/impl_base.hpp#L155) is called, which resets the compression stream(including the sliding window).  
  
**So the fix is to use `zlib::Flush::sync` instead of `zlib::Flush::full`, [here](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/websocket/detail/impl_base.hpp#L140)**  
  
The explanation for this can be found in the [zlib documentation](https://zlib.net/manual.html):  
  
> If flush is set to Z_FULL_FLUSH, all output is flushed as with Z_SYNC_FLUSH, and **the compression state is reset** so that decompression can restart from this point if previous compressed data has been damaged or if random access is desired. **Using Z_FULL_FLUSH too often can seriously degrade compression.**  
  
**Z_SYNC_FLUSH:**  
  
> If the parameter flush is set to Z_SYNC_FLUSH, all pending output is flushed to the output buffer and the output is aligned on a byte boundary, so that **the decompressor can get all input data available so far**. (In particular avail_in is zero after the call if enough output space has been provided before the call.) **Flushing may degrade compression for some compression algorithms and so it should be used only when necessary**. This completes the current deflate block and follows it with an empty stored block that is three bits plus filler bits to the next byte, followed by four bytes (00 00 ff ff).   
  
They do recommend to avoid flushing(`Z_FULL_FLUSH`), and they also state that when you flush, it results in resetting the compression state.  
  
**Warnings:**  
  
 * I cannot guarantee that this is the correct fix or it won't break other things, all I can say is that for the tests that I've done, it behaved correctly.  
 * I have no idea on how it will impact the library, if it will never call write with `zlib::Flush::full`, as you can see [here](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/zlib/detail/deflate_stream.ipp#L472), when passing it, it does some resets. So the question that comes to mind is: Should we call that full flush from time to time?  
 * The only tests that I've made are manual tests, while capturing traffic with wireshark and confirming that it sends the correct data.  
 * The client was able to decompress the data correctly, the client also used the beast library(no additional changes were required in the beast library).  
 * I've tested this with the latest version of beast(develop branch)

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-09-23 13:57:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2313#issuecomment-925840830  

Many thanks, I've started a full CI run against your PR.  
We'll know in an hour or so whether it breaks anything :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-09-23 14:33:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2313#issuecomment-925873746  

Amazing job tracking this down, and thank you for the detailed write-up !!! I believe your fix is correct.

---

## Comment 6

> Username: chreniuc  
> Created at: 2021-10-07 11:11:35 UTC  
> Updated at: 2021-10-07 11:45:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2313#issuecomment-937692609  

We did some stress tests with this fix, just to see the impact it has.  
  
We've started two instances of our server, both receiving the same input and generating the same output, one with the beast version that doesn't have the fix and one with the fix. Below are the stats that we've obtained(the instances were stressed and ran for 28 hours, we did this multiple times in the past two weeks):  
  
**Conclusion 1**: Without having this fixed, there was a **40%** reduction of our bandwidth, with the fix it got up to **60%**. So this fixed reduced the bandwidth with an additional **20%**.  
  
**Conclusion 2**: In our case, the best compression level was **5**(instead of **8**, which is the default). Using **8**, would result in a decrease of **2%** of the payload but with an addition cost of cpu of **51%**. **Note: This was in our use case, I cannot guarantee that the same will happen with every app.**  
  
**Note:** Our messages are binary data(protobuf messages serialized).  
___  
  
Details:  
  
**Bandwidth reduction**  
  
Abbreviations:  
  
 - [R] - per message deflate disabled - raw payload  
 - [NS] - not streaming compression, without streaming compression(per message deflate enabled)  
- [S] - with streaming compression, with the proposed fix in beast(per message deflate enabled)  
  
Total payloads:  
  
 - [R] - 277 GB sent  
 - [NS] - 170 GB sent - ratio: `1.6` `40%` improvement  
 - [S] - 106 GB sent - ratio(NS): `1.6`, `40%` improvement; ratio(R): `2.6`, `60%` improvement  
  
We've generated around 1 billion to 800 million of messages in these 28 hours.  
  
**Best Compression Level in our use case**  
  
*Note: We've used compression **level 5**.*  
  
We did some tests and we've noticed that having a higher compression level has almost no benefits, but only drawbacks(in our case, the cpu increased a lot):  
  
Abbreviations:  
  
 - [R] - per message deflate disabled  
 - [L5] - per message deflate enabled, compression level 5  
 - [L8] - per message deflate enabled, compression level 8  
   
   
Total payloads:  
  
 - [R] - 238 GB sent  
 - [L5] - 92.2 GB sent - ratio: `2.6`, `62%` improvement  
 - [L8] - 84.5 GB sent - ratio(L5): `1.1`, `9%` improvement; ratio(R): `2.8`, `65%` improvement  
  
Cpu usage:  
  
- [R] - avg: 118, max: 205  
- [L5] - avg: 156, max: 314 - `32%` increase  
- [L8] -  avg: 213, max: 420 - `40%` increase(L5), `83%` increase(R)  
  
We've generated around 1 billion to 600 million of messages in these 28 hours.  
  
So for a **3%** improvement to the compressed size, we pay an additional **51%** of cpu.

---

## Comment 7

> Username: ddevienne  
> Created at: 2021-10-07 11:36:14 UTC  
> Updated at: 2021-10-07 11:43:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2313#issuecomment-937708609  

Whether or not the cost of additional CPU load for better compression is worth it, depends on your network speed.  
At 1Gb/s (i.e. typical LAN), typically not; at lower speed (e.g. 100Mb/s, on the WAN), it can pay off.  
  
The externally defined protocol I implemented does not use WebSocket deflate, but negotiates compression of the  
message "_bodies_", excluding a custom protocol-specific header, _on top_. This allows to use other compression algos: I tested  
ZLib, SLZ (ZLib compatible faster compressor, used by HAProxy), and LZ4. At LAN speeds, only LZ4 is worth it in my case,  
and even then, when the network gets faster, it no longer helps. (e.g. Azure gets up to 30Gb/s). At lower-speeds, ZLib wins.  
  
FYI, and FWIW.  
  
See for example https://github.com/lz4/lz4/issues/1006#issuecomment-869757267 for network-bandwidth dependent benefits of using various LZ4 levels.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-10-07 11:38:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2313#issuecomment-937709817  

Compression benefits are very situational. The side doing the compression pays a lot of CPU, while the side that is decompressing spends very little CPU but saves a bunch of bandwidth. It depends on the use-case if this is a net win.

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-10-07 11:46:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2313#issuecomment-937715285  

This is fascinating
