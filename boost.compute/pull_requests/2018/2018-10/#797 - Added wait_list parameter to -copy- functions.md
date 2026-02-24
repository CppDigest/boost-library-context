# #797 Added wait_list parameter to "copy" functions [Merged]

> Username: antonmyagkov  
> Created at: 2018-10-17 09:19:10 UTC  
> Updated at: 2019-02-10 15:29:49 UTC  
> Merged at: 2019-02-10 15:29:49 UTC  
> Closed at: 2019-02-10 15:29:49 UTC  
> Url: https://github.com/boostorg/compute/pull/797  

It's not possible to use current "copy" functions with CL_QUEUE_OUT_OF_ORDER_EXEC_MODE_ENABLE option in command queue, because wait_list parameter is missing.

---

## Comment 1

> Username: keryell  
> Created_at: 2018-10-24 02:10:22 UTC  
> Url: https://github.com/boostorg/compute/pull/797#issuecomment-432486311  

Quite pervasive but it looks good,

---

## Comment 2

> Username: jszuppe  
> Created_at: 2018-10-24 04:51:01 UTC  
> Url: https://github.com/boostorg/compute/pull/797#issuecomment-432511972  

Wouldn't it be better if it was done only for `copy_async`? Because, honestly, what's the help if you use `copy` which does not give you an event with out-of-order queue?

---

## Comment 3

> Username: Ulfgard  
> Created_at: 2018-10-24 07:39:09 UTC  
> Url: https://github.com/boostorg/compute/pull/797#issuecomment-432547156  

It still helps. if you have an OoO-Queue, you might still want a blocking write operation ("copy back to device after you are done computing") but this has to wait until all relevant operations are done. This is especially interesting in a multi-thread context where each thread enqueues items independently into the same queue to ensure that the device always has enough to work on. In this case, doing explicit synchronization of the queue is harmfull to performance, synchronous copy is not.  
________________________________  
From: Jakub Szuppe [notifications@github.com]  
Sent: Wednesday, October 24, 2018 6:51 AM  
To: boostorg/compute  
Cc: Subscribed  
Subject: Re: [boostorg/compute] Added wait_list parameter to "copy" functions (#797)  
  
  
Wouldn't it be better if it was done only for copy_async? Because, honestly, what's the help if you use copy which does not give you an event with out-of-order queue?  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/compute/pull/797#issuecomment-432511972>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AOWTBq4o04NKCSKKSaar1Fl_PgwbJ0tqks5un_G4gaJpZM4Xje6z>.

---

## Comment 4

> Username: jszuppe  
> Created_at: 2018-10-25 20:32:13 UTC  
> Url: https://github.com/boostorg/compute/pull/797#issuecomment-433195354  

Right, I just don't want to run into a situation where we have multiple different ways async API is done in Boost.Compute. That's why I am just wondering if it's maybe better to have only `*_async` functions to work with OoO queues for now. However, I'm not saying 'no' to this change. @kylelutz What do you think?  
  
Anyway, I would need to revive our CI and test it on a few platforms (I think full coverage is: Mac, NV, AMD, Intel, and POCL; IMHO can be limited to 1.2) to make sure it's all right. If anyone can help with that I'll be grateful.

---

## Comment 5

> Username: Ulfgard  
> Created_at: 2018-10-26 09:28:32 UTC  
> Url: https://github.com/boostorg/compute/pull/797#issuecomment-433347040  

Without a wait_list, synchronous operations are impossible to use safely in an OoO context. In OpenCL, all operations except copies are asynchronous and synchronous copies have to wait until those operations are done. This little detail is abstracted away in an in-order queue since there is an implicit ordering of events(i.e. wait_list=all currently unfinished operations). In OoO we have to make this ordering explicit, otherwise the copy takes place immediately once the memory bus is free.

---

## Comment 6

> Username: coveralls  
> Created_at: 2018-12-27 16:05:24 UTC  
> Updated_at: 2019-02-10 12:19:00 UTC  
> Url: https://github.com/boostorg/compute/pull/797#issuecomment-450179684  

[![Coverage Status](https://coveralls.io/builds/21554303/badge)](https://coveralls.io/builds/21554303)  
  
Coverage increased (+0.004%) to 84.024% when pulling **3de4bbaa0be5720d8383985434b9ecd2d0f65f6d on u-s:feature/copy-with-wait-list** into **924ed68562b71a2e229a2aca0cb06f691294d203 on boostorg:develop**.

---
