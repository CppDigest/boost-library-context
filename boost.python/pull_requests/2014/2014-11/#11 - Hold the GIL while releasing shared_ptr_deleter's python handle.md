# #11 Hold the GIL while releasing shared_ptr_deleter's python handle. [Closed]

> Username: jloy  
> Created at: 2014-11-03 22:41:47 UTC  
> Updated at: 2024-03-05 19:37:54 UTC  
> Closed at: 2024-03-05 19:37:54 UTC  
> Url: https://github.com/boostorg/python/pull/11  

Python requires that the GIL be held while releasing objects, but requiring  
that all clients of boost::shared_ptr-held types do so explicitly is  
error-prone.  In the case where the wrapped type is consumed in a library that  
you don't control, it might not even be possible to do correctly.  Instead,  
have shared_ptr_deleter acquire the GIL on client's behalf at exactly the time  
when it is necessary.  
  
There's already an open trac issue for this (https://svn.boost.org/trac/boost/ticket/8290)  
but the documentation for handle::release doesn't specify that it never throws, so the  
patch in trac would fail to properly release the GIL state in that circumstance.

---

## Comment 1

> Username: hotgloupi  
> Created_at: 2014-11-04 07:58:50 UTC  
> Updated_at: 2014-11-04 14:28:13 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-61605065  

Hi,  
  
I don't understand why the GIL has to be explicitly held when down counting an object ref count, I've never done so, and never encountered a crash. Is it luck ? Or is it that bad things happen in the context of multiple threads (and ThreadStates) ?  
  
The patch looks good (~~except the "m_" thing which I've never seen elsewhere in boost codebase~~).

---

## Comment 2

> Username: jloy  
> Created_at: 2014-11-04 18:11:52 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-61686798  

Multiple threads is indeed the issue.  In our case it's not about multiple ThreadStates exactly, but rather no thread state.  We release the GIL when calling into purely C++ libraries so we can actually utilize multiple cores concurrently.  This sets the current thread state to `NULL`.  In that (seemingly) C++-only code path, dropping a `shared_ptr` might run the `shared_ptr_deleter`, which might wind it's way into [`subtype_dealloc`](https://hg.python.org/cpython/file/ee879c0ffa11/Objects/typeobject.c#l905) inside of Python itself, which wants to access the current `PyThreadState`, which is `NULL`.  This will crash.   Someone along the way needs to re-acquire the GIL before calling back into Python, establishing the proper current thread state.  
  
Luck can certainly also play a role.  We were using this code for years without issue until we started hitting a crash regularly following an update from Python 2.6 to 2.7.  
  
If you're not releasing the GIL yourself or shipping `shared_ptr`'s between Python and non-Python threads, it's likely that you're safe from this particular issue.  
  
Does that answer your questions?  Thanks for taking the time to look this over.

---

## Comment 3

> Username: hotgloupi  
> Created_at: 2014-11-04 18:33:07 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-61690159  

Thanks for such a detailed reply ! If I understand correctly, some C++ code  
that runs in a separate thread might drop a shared_ptr, which was the last  
reference of it, and thus calling indirectly the CPython API.  
  
Correct me if I'm wrong, but I see one problem with this patches: it  
imposes a GIL lock/unlock even on code instrumented by python (not in a  
thread), so this is a performance issue (in my case, threads never interact  
directly with python objects, yet).  
  
An optimization could be to check the thread state value before locking the  
GIL, so mono-threaded code won't suffer too much penalty, am I right ?  
​

---

## Comment 4

> Username: jloy  
> Created_at: 2014-11-05 20:23:19 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-61874773  

Yes, you are right that even single-threaded clients would pay the overhead of GIL acquire/release and your suggestion to elide the GIL operations in all cases that they're not necessary would be ideal.  Unfortunately, the thread state access API (e.g. [`PyThreadState_Get`](https://docs.python.org/2/c-api/init.html#c.PyThreadState_Get)) requires that the GIL be held before calling them.  We would have to hold the GIL in order to check if we have to hold the GIL.  
  
However, we can still do a little bit better than my original patch.  First, we can omit this entirely if the Python interpreter is compiled without threading support.  We could also make a runtime check to see if the client has called `PyEval_InitThreads` and omit the GIL access.  (Technically, we can only make this check in Python 2.4 or newer using [`PyEval_ThreadsInitialized`](https://docs.python.org/2/c-api/init.html#c.PyEval_ThreadsInitialized).  Do you happen to know what the minimum supported version for boost is?)  
  
I'll see if I can update the patch in the next couple of days.

---

## Comment 5

> Username: hotgloupi  
> Created_at: 2014-11-05 20:47:53 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-61878182  

Thanks for looking into it ! It was just thinking out loud. Anyway, the  
solution you propose sounds good to me.  
If I remember correctly, boost python supports at least python 2.2, but I'm  
pretty sure that nobody would object moving it to 2.4.  
In the worst scenario we could do this optimization only for python >= 2.4  
...  
  
In any case, it would be great if official contributors could have an eye  
on this !  
​

---

## Comment 6

> Username: jloy  
> Created_at: 2014-11-11 22:10:23 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-62631224  

Ok, I've updated things to work as described above.  Anyone that doesn't enable threading or is still using an older version of Python should be unaffected by this change.

---

## Comment 7

> Username: eudoxos  
> Created_at: 2015-09-27 09:49:52 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-143533586  

Great pull request. What is the status, is there a way to push it forward?   
  
For the record, link to old ticket: https://svn.boost.org/trac/boost/ticket/8290 and a discussion at c++-sig where some resistance was met.

---

## Comment 8

> Username: eudoxos  
> Created_at: 2016-10-23 17:06:40 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-255600384  

Ping?

---

## Comment 9

> Username: stefanseefeld  
> Created_at: 2016-10-23 17:16:40 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-255600956  

I'm aware of this PR. As has been pointed out earlier there were objections raised to this in http://code.activestate.com/lists/python-cplusplus-sig/17196/, as such a change may cause a deadlock.  
Can you convince me that this isn't the case ? If not, could the code be reworked a bit to make this new policy optional, so users could pick explicitly whether they want to use a lock or not ?

---

## Comment 10

> Username: eudoxos  
> Created_at: 2016-10-23 17:38:26 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-255602146  

I see the objection as ungrounded; if there were potential deadlock with this change, there would be very likely crash with the current state - when Python object is deleted (or in general, when Python API is called), the acting thread _must_ hold the GIL (I don't have citation now, but it is in Python docs written very clearly IIRC), otherwise other thread might simultaneously call Python API possibly causing crash, if both happen to touch the same memory structures.  
  
If the behavior were selectable via policies, I'd need some guidance how to do it; or maybe someone else knowing that template jungle (meant in a good way) better could code it quickly.

---

## Comment 11

> Username: nwhitehorn  
> Created_at: 2016-12-13 20:54:36 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-266858929  

The only way to reliably avoid deadlocks here would be to have shared_ptr_deleter add reset requests to a global queue that is processed when the GIL is acquired in the normal course of events. This would require a special boost python wrapper for the GIL that knows to check the queue, but that isn't so bad.

---

## Comment 12

> Username: eudoxos  
> Created_at: 2017-10-13 09:45:16 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-336404829  

@nwhitehorn, I don't see how this would cause deadlock. Repeating, if GIL is in the same thread, the lock is a no-op, so no deadlock. If another thread tries to acquire it, it will wait till it is released. If it is already held in another thread, the request for GIL will be pending until the other thread releases it.  
  
If there are cross-dependencies between threads leading to deadlock, then they would be crashing python as well (if not, only by accident).  
  
Please reconsider this. I am now solving another bug due to this erratic design decision, and I am not very happy about it. (It might well be that boost::python is past its zenith in terms of being set in stone including its mistakes, for the fear of not breaking existing broken code and pybind11 is the rising star. Who knows...)

---

## Comment 13

> Username: nwhitehorn  
> Created_at: 2017-10-13 14:31:36 UTC  
> Updated_at: 2017-10-13 23:49:13 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-336469962  

The issue is that your code can't acquire any *other* locks while holding the GIL, because the GIL acquisition via shared_ptr deleter can happen at unpredictable times. This is an issue because all boost::python code is called with the GIL held unless explicitly released.  
  
Suppose that you have a lock I will call A. The lock will be acquired in a C++ function called by boost::python I will call B(), but has not been yet -- though B() has started.  
  
Separately, some other thread (C) is running and holds A. A shared_ptr held by C() goes out of scope while still holding A, which happens to have come from Python. The shared_ptr deleter then tries to acquire the GIL. That blocks, since B() is holding the GIL still.  
  
Now B() tries the acquire lock A. That blocks on the completion of C() -- which is now held on the completion of B(), which is waiting for C(). And now you have a deadlock. Note that the only thing required for this to happen is that you have a lock -- any lock -- acquired by C++ code called into by boost python.  
  
The problem arises in that shared_ptr deleters run in unpredictable contexts, so acquiring big locks that may cover large blocks of code like GIL as part of shared_ptr operations is dangerous. Practically, one of the following needs to be true:  
1. You can never let a shared_ptr go out of scope when any locks besides the GIL are held. In general, this is near-impossible, but the reset queue idea I mentioned actually does let this happen.  
2. Make sure that any call into C++ from Python releases the GIL immediately, so that no C++ locks are ever acquired while holding the GIL. To make this reliable, this would need to get pushed into boost::python. All entry points into the interpreter then need to acquire the GIL, including explicit CPython calls not part of boost::python, which breaks the boost::python API.

---

## Comment 14

> Username: lightmare  
> Created_at: 2019-08-21 01:55:40 UTC  
> Url: https://github.com/boostorg/python/pull/11#issuecomment-523262764  

I think that potential for deadlocks is not a valid argument against this PR.  
  
In the example @nwhitehorn presented, whether `shared_ptr_deleter` acquires the GIL is secondary, the application is asking for trouble either way. As long as `shared_ptr_deleter` does not acquire the GIL, whoever released the GIL must make sure no shared pointers go out before it's re-acquired **(1)**. The thread C() dropped a pointer while not holding GIL -- `shared_ptr_deleter` now has a choice:  
  - try to acquire the GIL and hope it won't deadlock, or  
  - DECREF without the GIL and hope it won't crash.  
  
Besides, trying to acquire any other lock while holding the GIL sounds like a bad strategy. It's a classic example where two threads are trying to acquire two locks, but in different order: thread B() goes GIL-->A, thread C() goes A-->GIL. The GIL is probably the most globalest lock in the application, so it should be acquired last.  
  
**(1)** or more precisely, no shared pointers holding Python objects whose `tp_dealloc` is not thread-safe, e.g. containers (global trash can), unicode (global interned dict), float (global free list), ...  
  
Reset queue sounds neat, might even be easy to implement with Py_AddPendingCall, but it seems a bit overkill compared to this PR.

---
