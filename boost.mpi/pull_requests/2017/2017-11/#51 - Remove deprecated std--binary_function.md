# #51 Remove deprecated std::binary_function [Merged]

> Username: pavelkryukov  
> Created at: 2017-11-21 17:13:00 UTC  
> Updated at: 2017-11-25 08:38:00 UTC  
> Merged at: 2017-11-25 08:37:32 UTC  
> Closed at: 2017-11-25 08:37:32 UTC  
> Url: https://github.com/boostorg/mpi/pull/51  

std::binary_function was removed in C++17 and is no longer available while compiling with MS Visual Studio 2017

---

## Review 1 [Changes requested]

> Username: aminiussi  
> Created_at: 2017-11-25 07:15:52 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/mpi/pull/51#pullrequestreview-78449827  

Hi @pavelkryukov , did you had a chance to look at my code comment ?  
There seems to be a missing {

📁 include/boost/mpi/operations.hpp

```diff
 128 |- {
 139 |+ struct logical_xor
 140 |+   typedef T first_argument_type;
```

> Username: aminiussi  
> Created_at: 2017-11-22 13:51:43 UTC  
> Updated_at: 2017-11-25 07:31:05 UTC  
> Url: https://github.com/boostorg/mpi/pull/51#discussion_r152569426  

A { seems missing.


---

## Comment 2

> Username: pavelkryukov  
> Created_at: 2017-11-25 07:32:26 UTC  
> Url: https://github.com/boostorg/mpi/pull/51#issuecomment-346924425  

Hi. Sorry, there was no notification :-(. I've returned the bracket.

---

## Comment 3

> Username: aminiussi  
> Created_at: 2017-11-25 08:26:11 UTC  
> Url: https://github.com/boostorg/mpi/pull/51#issuecomment-346926529  

I'm running the tests, build went ok. Should be merged soon.  
Thanks for your patch.

---

## Comment 4

> Username: aminiussi  
> Created_at: 2017-11-25 08:38:00 UTC  
> Url: https://github.com/boostorg/mpi/pull/51#issuecomment-346927040  

Merged

---
