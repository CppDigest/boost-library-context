# #460 Corrected intel-win Intel 19.0 VC++ support and added optional <root> option in intel-win as in intel-linux [Merged]

> Username: eldiener  
> Created at: 2019-07-11 05:09:42 UTC  
> Updated at: 2021-10-02 21:13:57 UTC  
> Merged at: 2019-08-21 15:24:29 UTC  
> Closed at: 2019-08-21 15:24:29 UTC  
> Url: https://github.com/boostorg/build/pull/460  



---

## Comment 1

> Username: eldiener  
> Created_at: 2019-07-27 20:25:49 UTC  
> Updated_at: 2019-07-27 20:26:39 UTC  
> Url: https://github.com/boostorg/build/pull/460#issuecomment-515711215  

I am pretty sure this is good to go now, after a number of hiccups. No other PR is changing the jam files in this PR so it will not interfere with any previous PRs being accepted or not if it is merged. Will someone please look at this at their leisure and either merge it to 'develop' or approve it and I will merge it to 'develop' myself ? The PR solves some serious problems for me when testing with intel-win and clang-linux in Windows and having multiple versios of each compiler installed in their own directory structure. I think the PR would be very useful for Windows developers/testers even if VC++ is the compiler of choice for most developers. Although I am not touting Intel on Windows, I think most people realize that gcc and clang targeting gcc are much more C++ standard compliant compilers on Windows than VC++.

---

## Comment 2

> Username: eldiener  
> Created_at: 2019-08-15 06:49:18 UTC  
> Url: https://github.com/boostorg/build/pull/460#issuecomment-521535595  

One more needed fix dscovered in my testing.

---
