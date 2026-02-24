# #318 Fix container traits for msvc [Merged]

> Username: octopus-prime  
> Created at: 2017-12-07 12:25:11 UTC  
> Updated at: 2017-12-16 19:10:01 UTC  
> Merged at: 2017-12-16 19:10:01 UTC  
> Closed at: 2017-12-16 19:10:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/318  

- Fixes #298 and is based on https://github.com/boostorg/spirit/commit/45902cbb4e949e7543bcf48d3a4ed1247cab4f6b provided by @Kojoley   
- Added container_support.cpp to x3 test suite again

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-12-07 12:27:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/318#issuecomment-349953020  

That was unfriendly takeover. Good job.

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2017-12-07 12:34:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/318#issuecomment-349954512  

Your fix is 8 days old but still no PR.  
All the time the tests for container_support are not running.  
I would be happy if you could provide your fix and of course we close this PR without merge afterwards.  
  
It is not my intend to takeover your ideas! My only intend is to run all tests. Sorry!

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-12-07 12:49:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/318#issuecomment-349957646  

You are doing fine. Do not worry.

---

## Comment 4

> Username: octopus-prime  
> Created_at: 2017-12-15 12:30:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/318#issuecomment-351994428  

What should happen with this PR now?

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-12-15 13:18:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/318#issuecomment-352003620  

Trigger appveyor

---

## Review 6 [Commented]

> Username: octopus-prime  
> Created_at: 2017-12-15 19:18:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/318#pullrequestreview-83901065  

size_t{} was because 64bit.  
0 is unsigned int and 32bit.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2017-12-15 19:36:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/318#issuecomment-352093080  

> size_t{} was because 64bit.  
  
Imagine, I pass my custom `struct { void resize(std::int8_t); }` container.  
  
> 0 is unsigned int and 32bit.  
  
That's not right. Literal will be unsigned if you use `u` suffix and `int` is *at least* 16bit.

---

## Comment 8

> Username: octopus-prime  
> Created_at: 2017-12-15 19:52:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/318#issuecomment-352096600  

`signed int` you are right - and even worst...  
And at least 16bit, ok. On my machine 32bit...  
  
The main idea was to prove for existence of exact `reserve` signature in container class, wasn't it?  
  
So here is signature from std::vector: `void reserve( size_type new_cap )`  
From http://en.cppreference.com/w/cpp/container/vector/reserve  
  
So what about `T::size_type`? That's the exact type...  
  
And of course I think `0` is good enough - so `size_t{}` was too.

---

## Comment 9

> Username: Kojoley  
> Created_at: 2017-12-15 19:58:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/318#issuecomment-352097833  

That's what you propose:  
```cpp  
std::vector<int> v;  
v.reserve(std::vector<int>::size_type{123});  
```  
I wonder, you are doing it this way? It is crazy.

---

## Comment 10

> Username: octopus-prime  
> Created_at: 2017-12-15 20:04:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/318#issuecomment-352099113  

LOL. So I used `size_t`. May be `std::size_t` would be better.  
But `0` is fine. I imagine your custom `struct { void resize(std::int8_t); }` container.

---
