# #663 json stream: Fix out-of-bound memory access by using *start+size instead of *end [Closed]

> Username: enrico-samknows  
> Created at: 2021-12-31 13:10:08 UTC  
> Updated at: 2023-09-29 09:28:26 UTC  
> Closed at: 2023-09-29 09:28:25 UTC  
> Url: https://github.com/boostorg/json/pull/663  

Fix out-of-bound memory access by using start+size instead of a pointer to the first byte after buffer end.  
  
By using clang AddressSanitizer, I got memory access errors when calling serialize() because the end_ pointer refers to a memory byte that is not owned by anyone (it's actually the first byte after the buffer).  
This PR fixes the error for me by using start_ and size_ to track the buffer boundaries.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-12-31 14:06:47 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1003384804  

Why doesn't our asan target in CI report this?

---

## Comment 2

> Username: enrico-samknows  
> Created_at: 2021-12-31 14:17:57 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1003387480  

I used `ASAN_OPTIONS=detect_invalid_pointer_pairs=2`. Is yours set to 0?

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-12-31 14:24:05 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1003388780  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/663/pullrequest-condensed-fe75115.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/663/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/663/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-12-31 16:04:06 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1003408174  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/663/pullrequest-condensed-e099f9c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/663/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/663/pullrequest.html)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-01-01 06:27:31 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1003514194  

> I used ASAN_OPTIONS=detect_invalid_pointer_pairs=2. Is yours set to 0?  
  
Good question :) If it isn't set to 2 then we should set it to 2. @sdarwin ?

---

## Comment 6

> Username: enrico-samknows  
> Created_at: 2022-02-01 10:15:44 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1026678642  

Hello, any update on this? Is it worthwhile to you or should I close it?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-02-01 15:49:31 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1026991914  

Nope don't close this

---

## Comment 8

> Username: grisumbras  
> Created_at: 2023-06-08 13:23:41 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1582576009  

I fixed all asan failures I've managed to reproduce locally with the options you mentioned. Can you test current develop branch if your failures are reproducible still?

---

## Comment 9

> Username: enrico-samknows  
> Created_at: 2023-07-31 08:01:24 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1657868772  

> I fixed all asan failures I've managed to reproduce locally with the options you mentioned. Can you test current develop branch if your failures are reproducible still?  
  
hello, will I get your changes if I try Boost "Version 1.83.0 beta 1"?

---

## Comment 10

> Username: grisumbras  
> Created_at: 2023-07-31 13:04:06 UTC  
> Updated_at: 2023-07-31 13:04:17 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1658334818  

> hello, will I get your changes if I try Boost "Version 1.83.0 beta 1"?  
  
Yes.

---

## Comment 11

> Username: grisumbras  
> Created_at: 2023-09-29 09:28:26 UTC  
> Url: https://github.com/boostorg/json/pull/663#issuecomment-1740586484  

I'm closing this for now. If the problem still occurs, please reopen.

---
