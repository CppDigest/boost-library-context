# #61 Checked catching of forced_unwind [Closed]

> Username: notadragon  
> Created at: 2017-07-17 01:11:49 UTC  
> Updated at: 2018-05-26 05:20:33 UTC  
> Closed at: 2018-05-26 05:20:33 UTC  
> Url: https://github.com/boostorg/context/pull/61  

Stemming from an attempt to yield in a coroutine within a streambuf method in libstdc++, which has a catch(...) that just sets the bad bit on the ostream without propagating the exception back up, I would have been greatly helped by having the forced_unwind fail very fast when not caught instead of allowing the coroutine to continue until it failed in a different way (when it attempted to yield again) or worse, if the coroutine had silently succeeded after doing more unintended work after being destroyed.  
  
I've also moved all definitions of forced_unwind to exception.hpp so that the catch logic isn't duplicated, and potentially so forced_unwind can be made part of the 'public' api of context/coroutine2 so that code which wants to catch everything but rethrow forced_unwind can do so easily.  
  
I've added a test for destructors being called when a stack is unwound (fn6 in test_execution_context_v2.impl seemed to be intended for doing that, but wasn't invoked by any tests.)  I've left commented out a draft of a test for the catch detection, but since the catch detection triggers a terminate in a different stack I was not able to easily make that something testable by boost test.

---

## Comment 1

> Username: olk  
> Created_at: 2018-05-26 05:20:33 UTC  
> Url: https://github.com/boostorg/context/pull/61#issuecomment-392237797  

added - but needed some fixes because of merge conflicts  
ty

---
