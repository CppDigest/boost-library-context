# #101 - Support thread safety analysis thru special attributes [Closed]

> Username: KindDragon  
> Created at: 2016-10-19 13:30:18 UTC  
> Updated at: 2018-10-09 04:37:19 UTC  
> Closed at: 2018-10-09 04:37:19 UTC  
> Url: https://github.com/boostorg/thread/issues/101  

Clang and MSVC  
- Clang http://clang.llvm.org/docs/ThreadSafetyAnalysis.html#mutex-h  
- MSVC Static Analyzer https://docs.microsoft.com/en-us/visualstudio/code-quality/annotating-locking-behavior  
  
Would you merge PR with some of the attributes?

---

## Comment 1

> Username: viboes  
> Created at: 2016-10-22 10:29:36 UTC  
> Url: https://github.com/boostorg/thread/issues/101#issuecomment-255520625  

Hi, thanks for the pointers.  
  
Yes, PR will be welcome.  
  
I will suggest to provide some macros for Boost.Config that are used by Boost.Thread so that other libraries can profit of these macros.

---

## Comment 2

> Username: viboes  
> Created at: 2018-10-09 04:37:19 UTC  
> Url: https://github.com/boostorg/thread/issues/101#issuecomment-428056907  

Closed as no PR has been provided
