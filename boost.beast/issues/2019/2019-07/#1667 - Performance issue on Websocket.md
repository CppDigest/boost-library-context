# #1667 - Performance issue on Websocket [Closed]

> Username: vcouvert  
> Created at: 2019-07-25 13:56:30 UTC  
> Updated at: 2019-09-05 18:16:46 UTC  
> Closed at: 2019-09-05 18:16:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1667  

Hi,  
  
I try to use Beast to stream images over network using websockets (no compression, no SSL).  
Images are 1.2Mb and sending them (using write or async_write) takes more than 2 secondes.  
  
Do you think I made something wrong with my configuration? Or this kind of timing seems quite normal?  
  
Thanks for your help.

---

## Comment 1

> Username: vcouvert  
> Created at: 2019-07-25 14:17:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-515061976  

My bad, actually, compression was enabled...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-07-25 14:37:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-515070229  

How is the performance with compression off?

---

## Comment 3

> Username: vcouvert  
> Created at: 2019-07-25 14:54:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-515077596  

Hi Vinnie,  
  
Without compression, it takes about 12 to 13 ms (30% more than my other test based on https://gitlab.com/eidheim/Simple-WebSocket-Server).  
  
Vincent

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-07-25 17:02:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-515125895  

Hmm... that's a a little bit faster :)

---

## Comment 5

> Username: pdimov  
> Created at: 2019-07-26 14:43:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-515481471  

2 seconds for compressing 1.2 Mb sounds a bit too much.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-07-26 14:55:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-515485815  

```  
-- timing: gzip -c -9 test.tga >test.9.gz  
-- 482 ms  
  
-- timing: gzip -c -8 test.tga >test.8.gz  
-- 413 ms  
  
-- timing: gzip -c -7 test.tga >test.7.gz  
-- 403 ms  
  
-- timing: gzip -c -6 test.tga >test.6.gz  
-- 372 ms  
  
-- timing: gzip -c -5 test.tga >test.5.gz  
-- 365 ms  
  
-- timing: gzip -c -4 test.tga >test.4.gz  
-- 361 ms  
  
-- timing: gzip -c -3 test.tga >test.3.gz  
-- 300 ms  
  
-- timing: gzip -c -2 test.tga >test.2.gz  
-- 303 ms  
  
-- timing: gzip -c -1 test.tga >test.1.gz  
-- 324 ms  
```  
  
```  
26.07.2019  17:51         3,861,485 test.1.gz  
26.07.2019  17:51         3,818,402 test.2.gz  
26.07.2019  17:51         3,761,652 test.3.gz  
26.07.2019  17:51         3,835,921 test.4.gz  
26.07.2019  17:51         3,816,168 test.5.gz  
26.07.2019  17:51         3,809,486 test.6.gz  
26.07.2019  17:50         3,807,957 test.7.gz  
26.07.2019  17:50         3,806,475 test.8.gz  
26.07.2019  17:50         3,805,892 test.9.gz  
21.07.2016  15:27         6,220,818 test.tga  
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-07-26 15:58:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-515508022  

@vcouvert were optimizations turned on in your build?

---

## Comment 8

> Username: vcouvert  
> Created at: 2019-07-29 07:28:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-515877959  

@vinniefalco my code is built with gcc and -O2 flag.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-07-29 20:24:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516148182  

> I try to use Beast  
  
Which version of Beast?

---

## Comment 10

> Username: vcouvert  
> Created at: 2019-07-29 20:35:01 UTC  
> Updated at: 2019-07-29 21:12:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516151730  

@vinniefalco Beast 1.70.0  
With https://github.com/boostorg/beast/issues/1666 workaround (wr_compress forced to true)

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-07-29 22:21:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516184662  

> @vinniefalco Beast 1.70.0 With #1666 workaround (wr_compress forced to true)  
  
That's not 1.70.0, that's 1.71.0 beta. If you look `<boost/beast/version.hpp>` what number do you see there?

---

## Comment 12

> Username: vcouvert  
> Created at: 2019-07-30 08:52:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516328884  

When I reported this issue, I used 1.70.0 version and edited stream_impl.hpp to force compression.

---

## Comment 13

> Username: vcouvert  
> Created at: 2019-07-30 14:28:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516443988  

One more information, when using secured Websockets (WSS), it takes 25ms (instead of 13ms) to transfer the same 1.2Mb image. I hoped switching from WS to WSS would not have such impact on performances.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-07-30 15:43:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516476746  

When you use WSS, what is the exact declaration you are using for the websocket stream?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2019-07-30 15:44:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516477047  

> If you look <boost/beast/version.hpp> what number do you see there?  
  
You never answered this

---

## Comment 16

> Username: vcouvert  
> Created at: 2019-07-30 15:47:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516478244  

> When you use WSS, what is the exact declaration you are using for the websocket stream?  
  
boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>, true> stream_;

---

## Comment 17

> Username: vcouvert  
> Created at: 2019-07-30 15:48:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516478624  

> > If you look <boost/beast/version.hpp> what number do you see there?  
>   
> You never answered this  
  
Sorry :-)  
  
#define BOOST_BEAST_VERSION 248

---

## Comment 18

> Username: vinniefalco  
> Created at: 2019-07-30 16:35:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516496858  

Going from 13ms to 25ms when switching to SSL is actually quite good. Encryption is not cheap.

---

## Comment 19

> Username: vcouvert  
> Created at: 2019-07-30 17:02:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516506836  

> Going from 13ms to 25ms when switching to SSL is actually quite good. Encryption is not cheap.  
  
Yes you're right! But as I will increase images size in future versions I try to get the best :-) I switched to -O3 so I fear I can no more improve the timings. Next step: trying with clang...

---

## Comment 20

> Username: vinniefalco  
> Created at: 2019-07-30 17:35:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-516518199  

Unless you are compiling OpenSSL yourself, changing the optimization settings won't affect the code that performs TLS.

---

## Comment 21

> Username: stale[bot]  
> Created at: 2019-08-29 17:46:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-526290840  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 22

> Username: stale[bot]  
> Created at: 2019-09-05 18:16:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1667#issuecomment-528509932  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
