# #170 Define BOOST_STACKTRACE_LINK when BOOST_STACKTRACE_STATIC_LINK is defined. Closes #169. [Merged]

> Username: pdimov  
> Created at: 2024-06-21 23:37:41 UTC  
> Updated at: 2024-06-23 19:17:13 UTC  
> Merged at: 2024-06-23 19:17:13 UTC  
> Closed at: 2024-06-23 19:17:13 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/170  



---

## Comment 1

> Username: coveralls  
> Created_at: 2024-06-22 00:05:34 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/170#issuecomment-2183595803  

[![Coverage Status](https://coveralls.io/builds/68236905/badge)](https://coveralls.io/builds/68236905)  
  
coverage: 86.35%. remained the same  
when pulling **f32bd5a543ca703b5d42d992a58badc4961061f0 on feature/issue-169**  
into **6624a0aaebc0c881a1a15478f6ac14ae816c178c on develop**.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-06-22 16:51:26 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/170#issuecomment-2184104658  

I'm going to merge this if there aren't any objections, because currently the superproject CMake tests (which now include Stacktrace's) fail on CI because they use static linking.

---

## Comment 3

> Username: coveralls  
> Created_at: 2024-06-23 18:05:07 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/170#issuecomment-2185256359  

[![Coverage Status](https://coveralls.io/builds/68246496/badge)](https://coveralls.io/builds/68246496)  
  
coverage: 86.35%. remained the same  
when pulling **34e56c4e90f76e933d019b3ce824913957493f93 on feature/issue-169**  
into **6624a0aaebc0c881a1a15478f6ac14ae816c178c on develop**.

---

## Comment 4

> Username: coveralls  
> Created_at: 2024-06-23 18:05:50 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/170#issuecomment-2185256634  

[![Coverage Status](https://coveralls.io/builds/68246498/badge)](https://coveralls.io/builds/68246498)  
  
coverage: 86.35%. remained the same  
when pulling **34e56c4e90f76e933d019b3ce824913957493f93 on feature/issue-169**  
into **6624a0aaebc0c881a1a15478f6ac14ae816c178c on develop**.

---
