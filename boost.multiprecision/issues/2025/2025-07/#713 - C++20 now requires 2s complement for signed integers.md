# #713 - C++20 now requires 2s complement for signed integers [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-07-08 05:11:30 UTC  
> Updated at: 2025-07-10 16:04:48 UTC  
> Closed at: 2025-07-10 16:04:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/713  

https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/ints/cpp_int.html  
  
> That said it should be noted that there's no requirement for fundamental types to be 2's complement either - it's simply that this is the most common format by far.  
  
  
This is no longer true with C++20

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-07-08 17:42:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/713#issuecomment-3049793549  

Good catch Richard. I'm not entirely sure if we can make 1.89, but then the next one for sure.

---

## Comment 2

> Username: LegalizeAdulthood  
> Created at: 2025-07-10 16:04:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/713#issuecomment-3058064012  

Looks like this was corrected in c9afc1fb8cc448144338b4ef6986c114a9585efa so this can be closed.
