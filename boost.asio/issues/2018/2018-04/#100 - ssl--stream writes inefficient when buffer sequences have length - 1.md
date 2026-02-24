# #100 - ssl::stream writes inefficient when buffer sequences have length > 1 [Closed]

> Username: vinniefalco  
> Created at: 2018-04-27 00:07:44 UTC  
> Updated at: 2020-12-30 00:50:24 UTC  
> Closed at: 2020-10-23 20:12:52 UTC  
> Url: https://github.com/boostorg/asio/issues/100  

The write algorithms for `ssl::stream` end up performing a separate call to the underlying stream's `write_some` or `async_write_some` function for each individual buffer in a buffer sequence. This has performance implications when the buffer sequence has more than one element. These are related:  
  
https://stackoverflow.com/questions/38198638/openssl-ssl-write-from-multiple-buffers-ssl-writev  
https://stackoverflow.com/questions/50026167/performance-drop-on-port-from-beast-1-0-0-b66-to-boost-1-67-0-beast  
https://github.com/boostorg/beast/issues/1108

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-28 18:24:10 UTC  
> Url: https://github.com/boostorg/asio/issues/100#issuecomment-385196259  

The class `flat_stream` in the Beast examples provides a work-around:  
https://github.com/boostorg/beast/pull/1110

---

## Comment 2

> Username: badfd  
> Created at: 2018-05-01 20:59:13 UTC  
> Updated at: 2018-05-01 21:21:04 UTC  
> Url: https://github.com/boostorg/asio/issues/100#issuecomment-385788441  

I have first-hand experience with the excessive number of system calls caused by `asio::ssl::stream`.  
  
For a fixed number of app-level HTTPS messages, here is the `strace -cf` output, both with and without `flat_stream` ...  
  
First, a count of the calls to `sendmsg` without `flat_stream` (865589):  
```  
% time     seconds  usecs/call     calls    errors syscall  
------ ----------- ----------- --------- --------- ----------------  
 94.07 2754.818654        1449   1901618    399724 futex  
  3.31   96.798247      649653       149           nanosleep  
  1.64   47.988316          55    865589           sendmsg  
  0.36   10.523860         170     61970           recvmsg  
  0.21    6.217944          50    123614           timer_settime  
  0.21    6.089656          49    123614           timer_create  
  0.21    6.084648          49    123614           timer_delete  
  o o o  
```  
  
And here is the count with `flat_stream` (62573):  
```  
% time     seconds  usecs/call     calls    errors syscall  
------ ----------- ----------- --------- --------- ----------------  
 95.17 1227.002496         883   1390306    212175 futex  
  3.19   41.115119      348433       118           nanosleep  
  0.76    9.787660         156     62672           recvmsg  
  0.24    3.078478          25    125018           timer_settime  
  0.24    3.032991          24    125018           timer_create  
  0.23    3.014875          24    125018           timer_delete  
  0.17    2.140766          34     62573           sendmsg  
  o o o  
```  
As you can see, for the `flat_stream` case, there's essentially a **1**-to-**1** ratio of calls to `sendmsg` vs calls to `recvmsg`. Conversely, for the non-`flat_stream` case, that same ratio is essentially **14**-to-**1**.

---

## Comment 3

> Username: TheStormN  
> Created at: 2020-10-23 19:49:33 UTC  
> Url: https://github.com/boostorg/asio/issues/100#issuecomment-715557533  

Hey all, is this issue still present in pure ASIO? I see that `ssl::stream` is now movable, but not sure if the scatter/gather performance is fixed?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-10-23 20:12:40 UTC  
> Url: https://github.com/boostorg/asio/issues/100#issuecomment-715568154  

I do believe this was fixed in Asio, here: https://github.com/boostorg/asio/commit/2f327be5fed7b19d8c027fbcbba3a8b883edfa16#diff-c71ff1a2ef349841e0fa204d7e13400aab4677ac8853d8b21081105dbb6f7f18

---

## Comment 5

> Username: ghost  
> Created at: 2020-12-30 00:50:23 UTC  
> Url: https://github.com/boostorg/asio/issues/100#issuecomment-752289330  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#607](https://github.com/chriskohlhoff/asio/issues/607).
