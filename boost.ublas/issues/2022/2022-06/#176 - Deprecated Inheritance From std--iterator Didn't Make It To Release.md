# #176 - Deprecated Inheritance From std::iterator Didn't Make It To Release [Open]

> Username: KurtAA42  
> Created at: 2022-06-09 13:41:43 UTC  
> Updated at: 2024-05-05 15:42:47 UTC  
> Url: https://github.com/boostorg/ublas/issues/176  

An UBLAS patch called:  "Remove deprecated inheritance from std::iterator #97" didn't make it into the released version of Boost.  
  
"std::iterator was deprecated in C++17 and removed in C++20. I replaced the inheritance with the 5 equivalent typedefs, even though they're not all used by ublas, for compatibility in case clients depend on them."  
  
If I copy the file latest development version of the boost/numeric/ublas/detail/iterator.hpp into my boost directory it fixes the issue when building with VS 2019 C++20.  
  
The fix was over 2 years ago and should have made it into Boost 1_75.  
  
-Kurt

---

## Comment 1

> Username: j2bbayle  
> Created at: 2023-11-17 18:33:20 UTC  
> Url: https://github.com/boostorg/ublas/issues/176#issuecomment-1816903975  

Any updates on this?  
  
Feels like this hack (adding back the deprecated and then removed `std::iterator` class) should be finally removed and the proposed fix (“Remove deprecated inheritance from std::iterator https://github.com/boostorg/ublas/pull/97”) included in the next upcoming release?  
  
For users relying on C++17, lots of very irritating deprecation warnings appear when using ublas.

---

## Comment 2

> Username: j2bbayle  
> Created at: 2023-12-01 10:36:31 UTC  
> Url: https://github.com/boostorg/ublas/issues/176#issuecomment-1835858030  

Anyone?

---

## Comment 3

> Username: Kojoley  
> Created at: 2024-04-29 05:31:27 UTC  
> Url: https://github.com/boostorg/ublas/issues/176#issuecomment-2081917559  

@pdimov @glenfe ublas has not had a single merge from develop to master in 5 years.

---

## Comment 4

> Username: glenfe  
> Created at: 2024-04-29 12:43:00 UTC  
> Url: https://github.com/boostorg/ublas/issues/176#issuecomment-2082630723  

@Kojoley yes, I have complained about this before.  
  
@bassoy Is uBLAS effectively unmaintained now? Do we need to find a new maintainer? If not, what is preventing a merge to master?

---

## Comment 5

> Username: pdimov  
> Created at: 2024-04-29 12:56:26 UTC  
> Url: https://github.com/boostorg/ublas/issues/176#issuecomment-2082656251  

What I remember from previous conversations on the matter is that the uBLAS team have their own ideas of how the develop and master branches need to work - develop requires C++20, whereas master still doesn't.  
  
But if so, someone needs to take care of cherry-picking fixes from develop to master.

---

## Comment 6

> Username: bassoy  
> Created at: 2024-04-29 14:56:58 UTC  
> Updated at: 2024-04-29 14:57:57 UTC  
> Url: https://github.com/boostorg/ublas/issues/176#issuecomment-2082967137  

@pdimov  and @glenfe .  We have developed a lot on the develop branch a lot of which is not merged to the master. We have made very good progress on the tensor development. Sorry   
  
I will take now full responsibility and start to solve the issues.

---

## Comment 7

> Username: bassoy  
> Created at: 2024-05-05 15:42:47 UTC  
> Url: https://github.com/boostorg/ublas/issues/176#issuecomment-2094855583  

> @pdimov and @glenfe . We have developed a lot on the develop branch a lot of which is not merged to the master. We have made very good progress on the tensor development. Sorry  
>   
> I will take now full responsibility and start to solve the issues.  
  
Pull request opened https://github.com/boostorg/ublas/pull/194.
