# #107 - Test failure in core.event [Closed]

> Username: roshanrags  
> Created at: 2014-05-07 20:59:47 UTC  
> Updated at: 2017-03-21 12:25:10 UTC  
> Closed at: 2017-03-21 12:25:10 UTC  
> Url: https://github.com/boostorg/compute/issues/107  

Weird test failure in `core.event`  
  
```  
6/97 Test  #6: core.event .............................***Failed    0.03 sec  
Running 2 test cases...  
/compute/test/test_event.cpp:45: error in "event_callback": check callback_invoked == true failed [true != true]  
  
*** 1 failure detected in test suite "TestEvent"  
```  
  
Sometimes it also fails with `false != true` which is understandable but `true != true` ?  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-08 04:01:30 UTC  
> Url: https://github.com/boostorg/compute/issues/107#issuecomment-42511337  

That is strange (especially with `true != true`). Does this happen consistently? What platform?  
  
Also, can you try inserting a call to `marker.wait()` before the check for `callback_invoked`?

---

## Comment 2

> Username: roshanrags  
> Created at: 2014-05-08 09:45:22 UTC  
> Url: https://github.com/boostorg/compute/issues/107#issuecomment-42530830  

Yeah, it happens quite consistently. Very rarely, it passes(I think only the first time running `ctest`). But other times it fails, and a huge part of that is with `true != true`. I'm running it on Intel HD 4000.  
  
Adding `marker.wait()` didn't make much of a change although I don't see `false != true` now.

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-05-11 01:21:48 UTC  
> Url: https://github.com/boostorg/compute/issues/107#issuecomment-42759526  

I was able to reproduce this and I think I figured it out.   
  
The problem was that Intel's GPU OpenCL implementation only executes the callbacks once the `cl_event` object is destroyed. I've updated the test to release the event object before the `callback_invoked` flag is checked. This line from the `clSetEventCallback()` documentation tipped me off:  
  
```  
All enqueued callbacks shall be called before the event object is destroyed.  
```  
  
The commit with the fix is here a107b9239f6d0f2a1d7427f0f83ee9f573e6bea0. Let me know if it fixes the issue for you too.

---

## Comment 4

> Username: roshanrags  
> Created at: 2014-05-16 15:45:27 UTC  
> Url: https://github.com/boostorg/compute/issues/107#issuecomment-43346725  

Didn't change anything unfortunately...

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-05-16 22:27:11 UTC  
> Updated at: 2014-05-16 22:27:19 UTC  
> Url: https://github.com/boostorg/compute/issues/107#issuecomment-43386026  

Hmm, strange. Is it still failing with both `true != true` and `true != false`? I'll look into this more.

---

## Comment 6

> Username: jszuppe  
> Created at: 2017-03-21 12:25:10 UTC  
> Url: https://github.com/boostorg/compute/issues/107#issuecomment-288062862  

I think it was fixed in https://github.com/boostorg/compute/commit/417cb03670adf4fa52dde2f0a6f0e4b89bf252ac (added a mutex and a condition_variable to ensure thread safety).
