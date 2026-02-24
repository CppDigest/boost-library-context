# #9 Boost permit object [Closed]

> Username: ned14  
> Created at: 2014-04-23 16:58:43 UTC  
> Updated at: 2016-05-25 21:27:13 UTC  
> Closed at: 2016-05-25 21:27:13 UTC  
> Url: https://github.com/boostorg/thread/pull/9  



---

## Comment 1

> Username: ned14  
> Created_at: 2014-04-23 17:00:23 UTC  
> Url: https://github.com/boostorg/thread/pull/9#issuecomment-41186920  

You may find the patch file from https://github.com/boostorg/thread/pull/9.patch of more use when reviewing. I'll start a load soak test program tomorrow which hammers the permit object for 24 hours, I'll send that when ready.

---

## Comment 2

> Username: viboes  
> Created_at: 2014-04-23 19:53:09 UTC  
> Url: https://github.com/boostorg/thread/pull/9#issuecomment-41206574  

This pull request contains also  
        Significantly tidied up the cruft in GetTicksCount64 patch from #80  
  
I can not merge it.   
  
BTW, why you don't provide patches for the develop branch?

---

## Comment 3

> Username: viboes  
> Created_at: 2014-04-23 19:55:20 UTC  
> Url: https://github.com/boostorg/thread/pull/9#issuecomment-41206841  

I have checkout your branch and pulled it and I'm getting the error on MacOs  
  
clang-darwin.compile.c++ ../../../bin.v2/libs/thread/test/pthread_permit_validity_p.test/clang-darwin-3.2xl/debug/threading-multi/sync/permits/pthread_permit_validity.o  
In file included from sync/permits/pthread_permit_validity.cpp:54:  
In file included from ../../../boost/thread/permit/unittests.cpp:46:  
In file included from ../../../boost/thread/permit/pthread_permit.h:48:  
../../../boost/thread/permit/c11_compat.h:335:122: error: use of undeclared identifier 'pthread_mutex_timedlock'  
inline int mtx_timedlock(mtx_t *PTHREAD_PERMIT_RESTRICT mtx, const struct timespec *PTHREAD_PERMIT_RESTRICT ts) { return pthread_mutex_timedlock((mtx), (ts)); }  
                                                                                                                         ^  
1 error generated.

---

## Comment 4

> Username: ned14  
> Created_at: 2014-04-23 20:17:45 UTC  
> Url: https://github.com/boostorg/thread/pull/9#issuecomment-41209297  

If you remember, my fork of your develop got me a very different develop branch to yours. There is like 146 files different.  
  
Mac OS doesn't implement pthread_mutex_timedlock? Crap. I'll think of a workaround tomorrow.

---

## Comment 5

> Username: ned14  
> Created_at: 2014-04-24 17:22:32 UTC  
> Url: https://github.com/boostorg/thread/pull/9#issuecomment-41307744  

Ok, OS X specific workaround added.

---

## Comment 6

> Username: ya1gaurav  
> Created_at: 2015-02-03 08:10:02 UTC  
> Url: https://github.com/boostorg/thread/pull/9#issuecomment-72610089  

I am able to build boost test library using-> ./bjam  
How can I build & run boost thread test suite on x86 ?  
I can build individual test case by linking to boost library, but whole test suite I am not able to build.

---

## Comment 7

> Username: ned14  
> Created_at: 2015-02-03 11:14:59 UTC  
> Url: https://github.com/boostorg/thread/pull/9#issuecomment-72633650  

I probably wouldn't worry about this particular feature branch for now. I'm going to use permit objects as the basis of a non-allocating future for AFIO v1.4 first, and then if the community agrees I'll port those futures to Boost.Thread v5 as a potential next-gen future object which is orders of magnitude faster than the current ones. In other words, I'll come back to this branch six months from now maybe.

---

## Comment 8

> Username: viboes  
> Created_at: 2016-05-25 21:27:13 UTC  
> Url: https://github.com/boostorg/thread/pull/9#issuecomment-221712873  

Not accepted.

---
