# #29 Add rvalue versions of static_pointer_cast, const_pointer_cast, dynamic_pointer_cast, reinterpret_pointer_cast.  [Merged]

> Username: cdglove  
> Created at: 2016-12-09 03:07:39 UTC  
> Updated at: 2016-12-12 03:55:09 UTC  
> Merged at: 2016-12-12 03:45:17 UTC  
> Closed at: 2016-12-12 03:45:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/29  

I came across this today when looking to optimize some code. This saves an atomic increment + decrement on each cast.  
  
Then I discovered there's a similar proposal for std here:  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0390r0.htm  
  
While I was in there, I noticed that there is no test for reinterpret_pointer_cast. Do we want one?

---

## Comment 1

> Username: pdimov  
> Created_at: 2016-12-09 04:01:29 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/29#issuecomment-265928214  

This looks useful, thanks. Can you please move the tests to their own file though (practice is to test new functionality in new tests) and add a test for dynamic_pointer_cast leaving the argument unchanged when it fails (because the pointer would be lost otherwise).  
  
We could use a test for reinterpret_pointer_cast as well. :-)

---

## Comment 2

> Username: cdglove  
> Created_at: 2016-12-09 13:22:31 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/29#issuecomment-266012494  

OK. I will move the tests, add one for unchanged argument to dynamic cast and add tests for reinterpret_pointer_cast (though possibly as a separate PR). Do we want those in a new file as well? I feel like reinterpret_pointer_cast should at least be lumped in with the other cast tests tests in shared_ptr_test.cpp for consistency. That, or we split all of the casts into their own files.

---

## Comment 3

> Username: pdimov  
> Created_at: 2016-12-09 15:16:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/29#issuecomment-266037830  

I'd prefer two new tests, one for the new functionality and one for the old reinterpret_pointer_cast.

---

## Comment 4

> Username: cdglove  
> Created_at: 2016-12-10 16:57:25 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/29#issuecomment-266222299  

OK.

---

## Comment 5

> Username: cdglove  
> Created_at: 2016-12-11 20:46:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/29#issuecomment-266306646  

I added a new test specifically for casting from rvalues. I'll create a separate PR with a new test for reinterpret_pointer_cast. I will also need a reinterpret_pointer_cast test for the rvalues, and I'm not completely sure, from an organization point of view, where to put it: here or the new reinterpret_pointer_cast_test. Neither are ideal, so please advise.

---

## Comment 6

> Username: pdimov  
> Created_at: 2016-12-11 22:28:19 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/29#issuecomment-266313136  

The reinterpret_pointer_cast test for rvalues should go here.

---

## Comment 7

> Username: pdimov  
> Created_at: 2016-12-12 03:44:23 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/29#issuecomment-266336979  

Thank you.

---

## Comment 8

> Username: cdglove  
> Created_at: 2016-12-12 03:55:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/29#issuecomment-266337933  

You're very welcome.

---
