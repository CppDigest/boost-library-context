# #2002 - websocket_client_async_ssl_system_executor sample exits before connecting [Open]

> Username: markus-bonk  
> Created at: 2020-06-30 15:56:13 UTC  
> Updated at: 2023-02-09 18:29:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2002  

### Boost 1.73 BOOST_BEAST_VERSION 290  
  
### Steps necessary to reproduce the problem  
  
Compile and run the Debug x86 configuration in the attached sample with visual studio 2019. This is the same sample as in the boost/beast deployment. Debug x86 has the command line arguments inserted.  
[websocket_client_async_ssl_system_executor.zip](https://github.com/boostorg/beast/files/4852848/websocket_client_async_ssl_system_executor.zip)  
  
The issue with the sample is the code line 258:  
net::system_executor().context().join();  
  
Contrary to the comment in the code this does not wait until the system_executor runs out of work but actively invokes scheduler_.work_finished(); which decrements the outstanding_work_ count and stops the scheduler_, resulting in the websocket session ending prematurely.  
  
As I am using the websocket_client_async_ssl_system_executor sample as the basis of my own work how one can wait on the system_executor?  
  
Thanks  
Markus

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-30 16:39:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-651910991  

Thanks for the heads -up @markus-bonk .  
  
Is there a particular reason you are looking to use the system_executor in preference to an io_context::executor_type?  
  
I only ask because there are moves afoot to remove system_executor from net.ts, as it has some counter-intuitive behaviour traits that can catch out the unwary developer.

---

## Comment 2

> Username: markus-bonk  
> Created at: 2020-07-01 05:24:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-652198492  

The main reason for going for the system_executor is/was that it hides the complete thread implementation/management: it looked like just a case of create the session, initiate it (three lines of code), then let the method do something else and come back later to see what was happening.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-08-01 09:00:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-667498873  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-08-08 09:37:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-670851321  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-09-07 13:20:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-688321928  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:32:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-1256867898  

@markus-bonk Can you try using the asio::thread_pool instead?

---

## Comment 7

> Username: dasigvr  
> Created at: 2023-02-08 11:23:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-1422439973  

I experience the same issue, but it presents only on Windows, not on Linux which is odd.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2023-02-08 14:51:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-1422717545  

I am ok with removing this example. The system executor is questionable.

---

## Comment 9

> Username: dasigvr  
> Created at: 2023-02-09 09:36:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-1423892858  

I like the ergonomics of system_executor since I don't have to pass it around like the io_context. However it's too buggy to be useful.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2023-02-09 18:29:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2002#issuecomment-1424637112  

Idiomatic ergonomics is to pass an _Executor_ to constructors.
