# #35 Add allocator support to map_std<> [Closed]

> Username: sehe  
> Created at: 2015-10-26 10:25:49 UTC  
> Updated at: 2015-11-24 11:37:01 UTC  
> Closed at: 2015-11-24 11:31:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/35  

There was a spurious comment  
  
```  
// FIXME should use ALLOC for map but std::allocator of std::pair<const I, T> and std::pair<I,T> fail to compile  
```  
  
From the commented code it looked like the ALLOC had been passed as the third type argument to `std::map<>`. Obviously, that needs to be the comparator, though, so my fix was to use  
  
```  
std::map<I, T, std::less<I>, ALLOC>  
```  
  
That compiles on all compilers I have (clang/gcc). It should compile on all standards compliant compilers/standard libraries.

---

## Comment 1

> Username: yimyom  
> Created_at: 2015-11-24 10:16:04 UTC  
> Url: https://github.com/boostorg/ublas/pull/35#issuecomment-159218609  

Hi, could you please resubmit your patch to ublas:develop and not boostorg:develop because we have a specific github project for the development of ublas

---

## Comment 2

> Username: sehe  
> Created_at: 2015-11-24 10:33:00 UTC  
> Url: https://github.com/boostorg/ublas/pull/35#issuecomment-159224079  

Where would that be. It seems you forgot to mention which gh project and a gh search didn't exactly turn up a clear winner

---

## Comment 3

> Username: yimyom  
> Created_at: 2015-11-24 11:21:10 UTC  
> Url: https://github.com/boostorg/ublas/pull/35#issuecomment-159235124  

sorry, indeed I forgot the link. Here it is: https://github.com/uBLAS/ublas

---

## Comment 4

> Username: sehe  
> Created_at: 2015-11-24 11:37:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/35#issuecomment-159238470  

@yimyom Done: https://github.com/uBLAS/ublas/pull/40

---
