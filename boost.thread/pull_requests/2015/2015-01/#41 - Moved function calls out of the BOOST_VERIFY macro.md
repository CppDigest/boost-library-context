# #41 Moved function calls out of the BOOST_VERIFY macro [Closed]

> Username: tempoz  
> Created at: 2015-01-28 16:11:54 UTC  
> Updated at: 2015-03-14 08:03:18 UTC  
> Closed at: 2015-02-17 22:40:20 UTC  
> Url: https://github.com/boostorg/thread/pull/41  

Due to the structure of some of the "assert" macros used by BOOST_VERIFY, it is unsafe to pass it function calls that are not idempotent (and thus good practice to not pass it function calls in general). In this particular instance, the pthread_mutex_lock call would be duplicated on a non-zero return from pthread_mutex_lock, resulting in a possible double lock. This issue was uncovered with Coverity.

---

## Comment 1

> Username: viboes  
> Created_at: 2015-01-28 18:40:28 UTC  
> Url: https://github.com/boostorg/thread/pull/41#issuecomment-71890105  

Do you have an example of assert an implementation that is in this case?

---

## Comment 2

> Username: pdimov  
> Created_at: 2015-01-29 15:58:02 UTC  
> Url: https://github.com/boostorg/thread/pull/41#issuecomment-72049131  

If this is true, it's a bug in BOOST_VERIFY and needs to be fixed, so I'll appreciate more information about it.

---

## Comment 3

> Username: pdimov  
> Created_at: 2015-01-29 16:09:37 UTC  
> Url: https://github.com/boostorg/thread/pull/41#issuecomment-72051406  

Note that verify_test.cpp already tests `BOOST_VERIFY( ++x )` and `BOOST_VERIFY( f(x) )`, where `f` is not idempotent (it increments `x`).

---

## Comment 4

> Username: tempoz  
> Created_at: 2015-03-13 20:01:04 UTC  
> Updated_at: 2015-03-13 20:01:55 UTC  
> Url: https://github.com/boostorg/thread/pull/41#issuecomment-79317740  

yes, /usr/include/assert.h on my machine includes  
  
``` cpp  
#define assert(expr)                                                   \  
((expr)                                                               \  
? __ASSERT_VOID_CAST (0)                                             \  
: __assert_fail (__STRING(expr), __FILE__, __LINE__, __ASSERT_FUNCTION))  
```

---

## Comment 5

> Username: pdimov  
> Created_at: 2015-03-13 20:09:12 UTC  
> Url: https://github.com/boostorg/thread/pull/41#issuecomment-79327797  

This only evaluates `expr` once. I'm willing to bet that `__STRING(expr)` does not evaluate `expr`, it just turns it into a string. I don't know why `#expr` is not used directly.

---

## Comment 6

> Username: tempoz  
> Created_at: 2015-03-13 20:21:12 UTC  
> Url: https://github.com/boostorg/thread/pull/41#issuecomment-79342614  

My apologies, you appear to be correct. I had actually not encountered stringification before, and so did not recognize it as an instance in which expr would not be evaluated. Marking as false positive in Coverity, and not bothering you about it anymore :P

---

## Comment 7

> Username: viboes  
> Created_at: 2015-03-14 08:03:18 UTC  
> Url: https://github.com/boostorg/thread/pull/41#issuecomment-80111476  

Thanks.

---
