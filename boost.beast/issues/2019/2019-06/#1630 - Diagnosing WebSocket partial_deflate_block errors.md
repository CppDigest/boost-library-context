# #1630 - Diagnosing WebSocket partial_deflate_block errors [Closed]

> Username: ceaglest  
> Created at: 2019-06-05 21:25:51 UTC  
> Updated at: 2021-05-06 21:20:46 UTC  
> Closed at: 2021-05-06 21:20:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1630  

Hello,  
  
First, let me say that I am impressed with the level of control afforded by Boost/Beast. Thank you very much for the hard work on this project!  
  
I was hoping to start a discussion around the possible causes for `partial_deflate_block` errors. After reviewing https://github.com/madler/zlib/issues/280, and the 1.7.0 source code I am somewhat confused as to whether these errors can occur normally as the result of a broken TCP connection, or if they are considered an exceptional circumstance / Server error / Client misconfiguration.  
  
I also looked at https://github.com/boostorg/beast/issues/923, but I don't believe the Server (an Application Load Balancer) would set BFINAL=1.  
  
### Version of Beast  
  
107000 / "1_70"  
  
### Steps necessary to reproduce the problem  
  
The code is unfortunately closed source, but is based upon the [websocket_client_coro_ssl example](https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/client/coro-ssl/websocket_client_coro_ssl.cpp), with permessage-deflate enabled.  
  
```  
        boost::beast::websocket::permessage_deflate deflate_options;  
        deflate_options.client_enable = true;  
        deflate_options.server_enable = true;  
        deflate_options.client_max_window_bits = 15;  
        deflate_options.memLevel = 4;  
        deflate_options.compLevel = 8;  
        stream_.set_option(deflate_options);  
  
// ....  
  
private:  
    boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>, true> stream_;  
```  
  
1. The Client establishes a WS/TLSv1.2/TCP stream with an ALB.  
2. Application layer handshakes above the WebSocket layer occur.  
3. WebSocket messages are exchanged between Client and Server.  
4. At a later time (seconds, or minutes later), the WebSocket closes with a `partial_deflate_block` error while reading an incoming message.  
  
The error seems to correspond with network connectivity issues on the device under test, but I am not entirely sure that this is the culprit. The default read and write buffer sizes are used for the WebSocket, and in the worst case the WebSocket messages sent or received would be 10s of KB in size, but are usually < 10 KB (without compression).  
  
I can provide pcaps and more detailed example of a failure, but it might be better to do so privately.  
  
### All relevant compiler information  
  
This error has been reported on iOS, where we compile boost using the Xcode 10.2.x toolchain. The build scripts used to compile boost can be found [here](https://github.com/twilio/twilio-boost-build).  
  
Best,  
Chris

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-06-06 00:29:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-499304354  

`partial_deflate_block` is basically like an assert. It means that Mark's assumption (described in zlib issue #280) was wrong.  
  
Beast's ZLib implementation is a C++ port of the original ZLib (you probably realized that already). The port makes it header-only and removes the "gzip" support (since it is not used for WebSocket, and would require additional tests). Issue #923 describes a deficiency in the implementation: Beast is not fully compliant with the RFC which describes the permessage-deflate extension.  
  
The only possibilities which can account for the error you are seeing are thus:  
  
1. The sender is sending malformed data  
2. Beast contains a bug  
3. The resolution to zlib issue 280 is incorrect.  
  
Network connectivity issues could not create the issue you are seeing, as connectivity problems usually do not cause data corruption. So if we are certain that the server is not defective (you indicated that your program is a client) then it is likely a bug exists in Beast. What we need is a small self-contained example program which demonstrates the problem. There are some things we can do first though:  
  
1. See if the problem can be replicated in Beast 1.69  
2. If you haven't already, disable 1.70 websocket timeouts and see if it can be replicated  
  
Otherwise, you will want to develop a small program that can reproduce the problem. This might sound complex at first but is actually much easier than you would expect. There is a nice utility class, `beast::test::stream` located here:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/_experimental/test/stream.hpp  
  
You can set up a small contrived client/server in a test program that uses a pair of `websocket::stream<test::stream>` connected together. They can handshake (see the beast websocket tests for examples of how to set this up or I can guide you) and then you can "stuff" the test stream with raw octets from your packet captures that result in the `partial_deflate_block` error.  
  
Once we have a clean sample of a clean series of compressed payloads that cause the error, we can take that sample can run it through the stock zlib decompressor and see what happens.  
  
I can try to answer any specific questions that you have to get this off the ground.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-07-06 01:17:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-508886802  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-07-13 02:16:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-511079390  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 4

> Username: dmorilha-twilio  
> Created at: 2019-07-17 22:01:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-512585101  

I am still in the laborious process of trying to reproduce it 👍

---

## Comment 5

> Username: dmorilha-twilio  
> Created at: 2019-07-17 23:13:54 UTC  
> Updated at: 2019-07-17 23:14:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-512602347  

@vinniefalco   
  
I could not find where in the deflate code `zs.total_out` is reset back to 0 or even decremented.   
  
In https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/read.hpp#L499, shouldn't the check then, be against the value `zs.total_out` holds before calling `impl.inflate` rather than against 0?  
  
Thanks,

---

## Comment 6

> Username: dmorilha-twilio  
> Created at: 2019-07-22 21:37:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-513964715  

I went ahead and made a PR for my proposed fix: https://github.com/boostorg/beast/pull/1663  
  
@vinniefalco @djarek It would be nice to get some validation as well as how I could write a test to validate it.  
  
Thanks,

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-07-23 03:23:10 UTC  
> Updated at: 2019-07-23 03:24:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-514038447  

> I could not find where in the deflate code zs.total_out is reset back to 0 or even decremented.  
  
Well, every time the struct is constructed, `total_out` is initialized to zero:  
https://github.com/boostorg/beast/blob/2ed1c92e036c70aca611315d476f60443d4cc28d/include/boost/beast/zlib/zlib.hpp#L106  
  
The struct is a local variable inside the loop so it will always start with `total_out==0`:  
https://github.com/boostorg/beast/blob/2ed1c92e036c70aca611315d476f60443d4cc28d/include/boost/beast/websocket/impl/read.hpp#L465

---

## Comment 8

> Username: dmorilha-twilio  
> Created at: 2019-11-06 18:29:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-550440883  

Hey, I tried with boost-1.71.0 and the problem remains, it looks like it is related with long all utf-8 strings, next steps are trying to come up with a test which consistently reproduces the error.

---

## Comment 9

> Username: dmorilha-twilio  
> Created at: 2021-03-15 22:18:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-799792640  

@vinniefalco @djarek finally got a chance to get back to this, it looks like the `read_some` impl invalidly raises the `partial_deflate_block` error code for when the last received deflated chunk does not fit into a single `buffers_front(cb_);` and a remainder is still pending to be inflated:  
  
```  
 -> partial deflate <-  
  sz.total_out: 54  
 21-03-12 14:07:14.560 TRACE: on_read(const beast::error_code &, const std::size_t)  
 21-03-12 14:07:14.560 DEBUG: WssTransport::onMessage (7680 bytes)  
 {"body" ... {"id":"b4b2bb07-586a-48e1-8064-a9c4b7423fe9","sid":"MT760583713619794ebf5adc89e42722e4"},{"id":"c6232956-79da-4726-835e-dfb37bbd35ed","sid":                            +> "MTea63d62fc35de55234fce1b8fd19a4f8"}]}  
 21-03-12 14:07:14.560 ERROR: JSON parsing failed with error "* Line 1, Column 7681  
   Missing ',' or '}' in object declaration  
 ```  
  
Wireshark had no problems to inflate it, the deflated payload was 69 bytes and after inflating it became 7734 = 7680 + 54.  
  
Here's the proposed fix: https://github.com/boostorg/beast/pull/2191

---

## Comment 10

> Username: dmorilha-twilio  
> Created at: 2021-05-06 21:18:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-833874168  

I believe this issue can be closed now...

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-05-06 21:20:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1630#issuecomment-833875409  

Thank you
