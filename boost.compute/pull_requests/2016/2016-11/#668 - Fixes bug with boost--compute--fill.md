# #668 Fixes bug with boost::compute::fill [Closed]

> Username: bkchr  
> Created at: 2016-11-05 14:54:32 UTC  
> Updated at: 2016-11-09 12:04:15 UTC  
> Closed at: 2016-11-09 12:04:02 UTC  
> Url: https://github.com/boostorg/compute/pull/668  

We need to wait for the event of clEnqueueFillBuffer, because the call  
is always async!

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-11-05 15:05:54 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258617402  

But `boost::compute::fill()` is not sync operation (in fact`fill_with_copy()`, which is used in `fill()` for all devices that support OpenCL 1.2, is async).

---

## Comment 2

> Username: bkchr  
> Created_at: 2016-11-05 15:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258617535  

And why exist "fill_async"? The function not returns the event, so it is not possible to use this function in a right way.

---

## Comment 3

> Username: bkchr  
> Created_at: 2016-11-05 15:10:35 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258617676  

fill_with_copy() is only used if the device does not support OpenCL1.2?

---

## Comment 4

> Username: jszuppe  
> Created_at: 2016-11-05 15:18:05 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258618148  

Yeah, I was about to correct my post. `fill_with_copy()` is used when device does not support OpenCL 1.2.

---

## Comment 5

> Username: bkchr  
> Created_at: 2016-11-05 15:22:45 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258618417  

But judging on the implementation and also that there is a "fill_async_with_copy", the fill_with_copy is also sync. Do you agree with me?

---

## Comment 6

> Username: jszuppe  
> Created_at: 2016-11-05 15:22:52 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258618428  

> And why exist "fill_async"? The function not returns the event, so it is not possible to use this function in a right way.  
  
`bc::sort()` also works async. In fact almost every algorithm which returns nothing (`void`) is async and user must use queue to sync either by using sync operations with the same queue or by explicitly calling `finish()` method etc.

---

## Comment 7

> Username: jszuppe  
> Created_at: 2016-11-05 15:30:06 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258618896  

> But judging on the implementation and also that there is a "fill_async_with_copy", the fill_with_copy is also sync. Do you agree with me?  
  
Yes, that may be misleading. I can say that is there is `_async()` then that function returns a `bc::future` object which contains an event referring to that operation. That's it.  
  
IMHO, the reason why not every function has it `_async` version is because not every function can easily have one event, because in a function many kernels can be called, multiple times and it's hard to provide one event on which user can wait etc.

---

## Comment 8

> Username: bkchr  
> Created_at: 2016-11-05 15:32:04 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258619036  

Okay, did not know this. However, judging from the fill and fill_async function names, the first is synchron in my understanding of the api. If that is not the case, okay, but then on function should be okay and not two to do the same? I had a bug because of using this function and it was very frustrating ^^

---

## Comment 9

> Username: jszuppe  
> Created_at: 2016-11-05 15:36:41 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258619327  

The reason why `fill()`, `sort()` and others are not sync is because that way user can write something like this:  
  
``` cpp  
queue q(..);  
vector<float> v(..);  
bc::fill(v.begin(), v.end(), q);  
bc::transform(v.begin(), v.end(), some_function, q);  
bc::sort(v.begin(), v.end(), q);  
q.finish(); <---- we wait only here  
```  
  
, and does not have to wait after each function. The C++ code executes, kernels are compiled and queued to execution etc, and we can wait on `q.finish()`. Otherwise program would just work slower.

---

## Comment 10

> Username: bkchr  
> Created_at: 2016-11-05 15:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258619571  

Yeah, I understand the reason, but why not use the fill_async? And why does this function even exist? The next problem with your code is the missing synchronization, based on events, because I do not want to execute the transform before the fill finished. For in order queues, your code should work, but for out of order queues the result is unpredictable.

---

## Comment 11

> Username: coveralls  
> Created_at: 2016-11-05 16:06:04 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258621111  

[![Coverage Status](https://coveralls.io/builds/8681931/badge)](https://coveralls.io/builds/8681931)  
  
Coverage remained the same at 83.338% when pulling **e90fd768d0117afdc807148871806e45bed5b8d1 on bkchr:fill_wait_fix** into **82f588f24ed2640bca4bf3f31dfcb06703d9bc13 on boostorg:master**.

---

## Comment 12

> Username: jszuppe  
> Created_at: 2016-11-05 16:40:07 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258623254  

You're right, for out of order queues that is incorrect. There is a issue  
opened for discussion about full asynchronous API. I guess nobody was  
really interested in this, usually people use in order queues.  
  
5 lis 2016 16:40 "bkchr" notifications@github.com napisał(a):  
  
> Yeah, I understand the reason, but why not use the fill_async? And why  
> does this function even exist? The next problem with your code is the  
> missing synchronization, based on events, because I do not want to execute  
> the transform before the fill finished. For in order queues, your code  
> should work, but for out of order queues the result is unpredictable.  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/compute/pull/668#issuecomment-258619571, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/ACmCBFVJEiAMmmTww5K6dBSf-lFnthcqks5q7KN6gaJpZM4KqSB5  
> .

---

## Comment 13

> Username: bkchr  
> Created_at: 2016-11-05 16:44:09 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258623536  

Sorry for asking and asking again :D But why exist an "async" function when both are async? That is real bad api design or is there any reason for that?

---

## Comment 14

> Username: jszuppe  
> Created_at: 2016-11-05 16:53:15 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258624109  

The reason is for some functions (like copy or fill) it's possible and very  
easy to give user an OpenCL event (wrapped in future) which refers to that  
operation. Usually it's when function calls only one kernel and it calls it  
only once. For the exact reason you need to ask Kyle.  
  
5 lis 2016 17:44 "bkchr" notifications@github.com napisał(a):  
  
> Sorry for asking and asking again :D But why exist an "async" function  
> when both are async? That is real bad api design or is there any reason for  
> that?  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/compute/pull/668#issuecomment-258623536, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/ACmCBO8ifo77bkAuC_XJTYwFIOYw7iNaks5q7LJbgaJpZM4KqSB5  
> .

---

## Comment 15

> Username: bkchr  
> Created_at: 2016-11-05 17:00:18 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-258624555  

Okay, then let's wait for an answer of Kyle :)  
  
Gesendet mit AquaMail für Android  
http://www.aqua-mail.com  
  
Am 5. November 2016 5:53:20 nachm. schrieb Jakub Szuppe   
notifications@github.com:  
  
> The reason is for some functions (like copy or fill) it's possible and very  
> easy to give user an OpenCL event (wrapped in future) which refers to that  
> operation. Usually it's when function calls only one kernel and it calls it  
> only once. For the exact reason you need to ask Kyle.  
>   
> 5 lis 2016 17:44 "bkchr" notifications@github.com napisał(a):  
>   
> > Sorry for asking and asking again :D But why exist an "async" function  
> > when both are async? That is real bad api design or is there any reason for  
> > that?  
> >   
> > —  
> > You are receiving this because you commented.  
> > Reply to this email directly, view it on GitHub  
> > https://github.com/boostorg/compute/pull/668#issuecomment-258623536, or mute  
> > the thread  
> > https://github.com/notifications/unsubscribe-auth/ACmCBO8ifo77bkAuC_XJTYwFIOYw7iNaks5q7LJbgaJpZM4KqSB5  
> > .  
>   
> ##   
>   
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/compute/pull/668#issuecomment-258624109

---

## Comment 16

> Username: kylelutz  
> Created_at: 2016-11-08 04:30:04 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-259042967  

Hi @bkchr,  
  
Thanks for bringing these issues up.  
  
At a high-level, all algorithms which interact solely with device memory (e.g. [`fill()`](http://www.boost.org/doc/libs/master/libs/compute/doc/html/boost/compute/fill.html), [`sort()`](http://www.boost.org/doc/libs/master/libs/compute/doc/html/boost/compute/sort.html)) are always executed asynchronously with respect to the host. This is done by enqueuing them for execution on a `command_queue` and returning immediately without blocking.  
  
In contrast, algorithms which interact with host memory (e.g. [`copy()`](http://www.boost.org/doc/libs/master/libs/compute/doc/html/boost/compute/copy.html)) are synchronous and will block until the operation is complete. This is to prevent errors which occur when they are given references to host memory which then goes out of scope after invoking the algorithm but before the operation can complete on the device.  
  
In order to allow better performance for particular applications, async versions of these algorithms exist (e.g. [`copy_async()`](http://www.boost.org/doc/libs/master/libs/compute/doc/html/boost/compute/copy_async.html)) which return events that can be waited on or added to wait lists for other kernel invocations. These are potentially unsafe operations which can cause errors if the user doesn't ensure the host memory remains valid until the operation on the device is complete.  
  
In order to provide a cohesive API, `_async` versions of all the rest of the algorithms were also added (e.g. [`fill_async()`](http://www.boost.org/doc/libs/master/libs/compute/doc/html/boost/compute/fill_async.html)) which return futures/events which can be waited on or passed as dependencies to `enqueue_nd_range_kernel()`. You're right in that `fill()` and `fill_async()` result in essentially the same code being executed, the only difference is `fill_async()` also returns a handle to the asynchronous operation (via a `future<void>`).  
  
As you note, the default behavior is currently incorrect for out-of-order command queues. Currently, users must explicitly serialize dependent kernel calls via `enqueue_barrier()` or the wait list argument to `enqueue_nd_range_kernel()`. This is an area I hope to improve, but haven't had the time to do it.  
  
Hope this helps.  
-kyle

---

## Comment 17

> Username: bkchr  
> Created_at: 2016-11-09 12:04:02 UTC  
> Updated_at: 2016-11-09 12:04:15 UTC  
> Url: https://github.com/boostorg/compute/pull/668#issuecomment-259399444  

Thanks for your answer. :) I should update my knowledge about boost compute.

---
