# #261 - Failed assertion in read_frame_op [Closed]

> Username: Boronak  
> Created at: 2017-02-10 15:30:08 UTC  
> Updated at: 2017-02-24 18:51:09 UTC  
> Closed at: 2017-02-24 18:51:09 UTC  
> Url: https://github.com/boostorg/beast/issues/261  

I am operating some long running websocket clients and every few days I get the assertion failed in do_close case of the switch statement:  
Assertion `! d.ws.wr_block_' failed.  
Is this a bug in the library or does it imply I am doing something wrong?  
  
This was beast/websocket/impl/read.ipp line 454 as of commit 1ab7a2f04ca9a0b35f2032877cab78d94e96ebad.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-10 15:36:35 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-278976787  

This means you are trying to close when you have an active write. Close counts as a write, you can't do both simultaneously.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-02-10 15:37:06 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-278976931  

I should add some comments around that assert, it happens semi-often.

---

## Comment 3

> Username: Boronak  
> Created at: 2017-02-10 15:53:22 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-278981287  

I am not calling any close function. Is there something else that triggers the close?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-02-10 15:58:16 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-278982669  

If you have a read operation pending, and a close frame is received, the implementation will automatically send a close frame in response. Although a write is performed at the low level, the implementation coordinates with caller-initiated writes to ensure that there is no block (`wr_block_` is a flag indicating that a write operation is in progress).  
  
It is possible, although unlikely, that a bug exists. I will investigate. This assertion can also happen if you attempt two writes at the same time. In the meanwhile, if you want to help track this down please review your code and make absolutely certain that you are not trying to perform two writes at the same time.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-02-10 16:16:46 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-278987720  

Upon reviewing the asynchronous read implementation, I can see how there might be a small window allowing a race condition. Please give this branch a try and let me know if any of the new asserts go off:  
https://github.com/vinniefalco/Beast/commits/fix

---

## Comment 6

> Username: Boronak  
> Created at: 2017-02-10 16:57:49 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-278999166  

I'm running the new branch now.

---

## Comment 7

> Username: felipealmeida  
> Created at: 2017-02-15 23:11:07 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-280171618  

I'm having a similar problem. But it crashes instead. Using the fix branch I still get crashes in read.ipp:661 calling maybe_invoke() and d.ws.wr_block_ is filled with trash.  
  
I never call close nor delete the connections. I always have a pending read, but writes are made synchronously and I only have one thread.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-02-15 23:11:40 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-280171758  

@felipealmeida Did you run the debug version?

---

## Comment 9

> Username: felipealmeida  
> Created at: 2017-02-15 23:12:32 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-280171970  

I'm always compiling with debug, yes.

---

## Comment 10

> Username: felipealmeida  
> Created at: 2017-02-15 23:12:54 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-280172061  

I'm using C++1y in GCC

---

## Comment 11

> Username: felipealmeida  
> Created at: 2017-02-15 23:14:26 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-280172464  

The problem is always reproducible in my case. I'm not sure it is a Beast bug however.

---

## Comment 12

> Username: felipealmeida  
> Created at: 2017-02-15 23:27:29 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-280175556  

OK. So I'm stupid. I'm moving the websocket stream while a pending operation exists, by using a std::vector.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-02-15 23:30:07 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-280176210  

Ah, you can't move websocket streams while there are pending operations :) Good catch. But I still think there's a bug from the original description in this issue. I have some ideas about it, I will push a possible fix shortly.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-02-15 23:58:28 UTC  
> Updated at: 2017-02-15 23:58:36 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-280182427  

I rewrote history in the **fix** branch to include a possible fix, please try it and let me know if the asserts go away (don't forget to run a debug build!)  
https://github.com/vinniefalco/Beast/commits/fix

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-02-17 23:17:48 UTC  
> Url: https://github.com/boostorg/beast/issues/261#issuecomment-280793115  

So...I guess you haven't seen the assert anymore?
