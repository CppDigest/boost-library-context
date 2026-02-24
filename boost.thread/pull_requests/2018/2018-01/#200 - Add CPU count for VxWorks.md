# #200 Add CPU count for VxWorks [Merged]

> Username: kuhlenough  
> Created at: 2018-01-12 06:11:46 UTC  
> Updated at: 2018-01-12 19:44:06 UTC  
> Merged at: 2018-01-12 19:43:44 UTC  
> Closed at: 2018-01-12 19:43:44 UTC  
> Url: https://github.com/boostorg/thread/pull/200  

OS specific API required

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2018-01-12 06:58:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/200#pullrequestreview-88392496  

📁 src/pthread/thread.cpp

```diff
 571 |+         return(i);
 572 |+   #else  
 573 |+         return (__builtin_popcount(set) );
```

> Username: viboes  
> Created_at: 2018-01-12 06:58:25 UTC  
> Updated_at: 2018-01-12 07:00:14 UTC  
> Url: https://github.com/boostorg/thread/pull/200#discussion_r161151332  

Does it means that when we use VxWorks, we have always a compiler that has this intrinsic?  
With with compiler has you tested?  
Maybe we need to do the opposite, check if __builtin_popcount is available, isn't it?  
Wondering if there isn't already a popcount in Boost.

> Username: kuhlenough  
> Created_at: 2018-01-12 16:38:21 UTC  
> Updated_at: 2018-01-12 16:38:22 UTC  
> Url: https://github.com/boostorg/thread/pull/200#discussion_r161268882  

VxWorks is packaged with one of 4 validated compilers depending on the board support package and processor, so I know that Intel ICC, GCC, and llvm/clang have __builtin_popcount() and  Diab compiler (__DCC_) does not.  I didn't find a Boost macro for popcount() but I agree its a cleaner solution.

> Username: viboes  
> Created_at: 2018-01-12 19:43:32 UTC  
> Url: https://github.com/boostorg/thread/pull/200#discussion_r161309734  

Okay, I accept the PR. We will adapt if someone else need something else.

> Username: viboes  
> Created_at: 2018-01-12 19:44:06 UTC  
> Url: https://github.com/boostorg/thread/pull/200#discussion_r161309859  

Thanks for the PR.


---
