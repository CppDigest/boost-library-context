# #179 bug in t1's size [Closed]

> Username: madhur4127  
> Created at: 2020-01-02 08:34:35 UTC  
> Updated at: 2020-01-02 12:53:56 UTC  
> Closed at: 2020-01-02 12:53:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/179  

t1's size must be `2*n+2` because both t2 and t3 could be of size `n+1`. The test case that fails is square of `2^20000-1` because it causes resize (81 -> 82) and SIGABRT.  
  
Also, made the `storage` tight.

---

## Comment 1

> Username: madhur4127  
> Created_at: 2020-01-02 08:41:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/179#issuecomment-570145720  

I think we should add this as a test (all 1s) because it is the worst case for the algorithm and it tests  
- time  
- space  
- correctness  
  
But please make sure that the exponent is big enough to make recursion deep.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-01-02 12:53:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/179#issuecomment-570199132  

Nod, we've both been looking at this I think.  I think this is now fixed (and tested) on this branch.

---
