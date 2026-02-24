# #38 fix narrowing conversions [Merged]

> Username: DanielaE  
> Created at: 2017-04-22 16:42:22 UTC  
> Updated at: 2017-12-26 16:27:34 UTC  
> Merged at: 2017-05-04 01:14:29 UTC  
> Closed at: 2017-05-04 01:14:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/38  

Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-04-22 16:44:04 UTC  
> Url: https://github.com/boostorg/algorithm/pull/38#issuecomment-296385896  

I have these changes in my take of Boost.Algorithm for the last three years now without any noticable negative effects in real-world projects.

---

## Comment 2

> Username: zamazan4ik  
> Created_at: 2017-04-30 00:01:11 UTC  
> Url: https://github.com/boostorg/algorithm/pull/38#issuecomment-298202094  

About your commit - Knuth-Morris-Pratt is wring. Difference type can be unsigned type. You can see line `j = -1`   
If we have unsigned type, it can broke algorithm.

---

## Comment 3

> Username: DanielaE  
> Created_at: 2017-04-30 16:12:47 UTC  
> Url: https://github.com/boostorg/algorithm/pull/38#issuecomment-298241207  

Please let me comment on your objection:  
  
- have a look at lines 99, 163, and 181. The skip vector's element type is `difference_type` and it's first element is initialized to -1 right before the loop which builds the skip vector. AFAICT the algorithm used here is the one described f.e. [here in Wikipedia](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm), which also requires a type which can hold a value of -1.  
  
- there are other places in the implementation which silently assume `difference_type` to be a signed type (f.e. line 147).  
  
- the documentation explicitly states that the iterator category of the algorithm's input arguments needs to be random access, which implies (by the standard, check the return type of `std::distance`) is a signed type.  
  
So, IMHO, my fix to get rid of narrowing warnings in 64 bit code where `difference_type` is wider than `int` is sound.  
  
Ciao, Dani

---

## Comment 4

> Username: zamazan4ik  
> Created_at: 2017-04-30 21:22:51 UTC  
> Updated_at: 2017-04-30 21:23:06 UTC  
> Url: https://github.com/boostorg/algorithm/pull/38#issuecomment-298258077  

@DanielaE you are right. Sorry for annoying.  
@mclow Can you merge it?

---
