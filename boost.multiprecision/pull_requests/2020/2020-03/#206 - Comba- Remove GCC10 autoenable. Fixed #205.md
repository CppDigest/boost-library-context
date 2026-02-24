# #206 Comba: Remove GCC10 autoenable. Fixed #205. [Merged]

> Username: madhur4127  
> Created at: 2020-03-30 12:08:25 UTC  
> Updated at: 2020-04-01 17:24:20 UTC  
> Merged at: 2020-04-01 17:18:14 UTC  
> Closed at: 2020-04-01 17:18:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/206  

Fixes #205

---

## Review 1 [Approved]

> Username: pabristow  
> Created_at: 2020-03-30 13:28:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/multiprecision/pull/206#pullrequestreview-383852875  

Looks like it will do the trick, but I'm a GIThub novice still :-(  I'm not clear if and how can I pull this into my develop to check it out?  (including trying define BOOST_MP_COMBA )  
  
(The error was actually triggering by running a Boost.Math example that uses multiprecision to get a reference value).  
  
(Did you also mean to remove result.normalize() ..? )

---

## Comment 2

> Username: madhur4127  
> Created_at: 2020-03-30 13:59:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/206#issuecomment-606016498  

> Looks like it will do the trick, but I'm a GIThub novice still :-( I'm not clear if and how can I pull this into my develop to check it out?   
  
You can use my repository: [madhur4127/issue205](https://github.com/madhur4127/multiprecision/tree/issue205)  
1. Just add the remote to my fork to your repo (using `git remote add <some_name> https://github.com/madhur4127/multiprecision.git`). You'll refer to my repo using this handle `<some_name>`.  
2. Merge my `issue205` branch to your `develop` by using `git merge <above_name>/issue205` when your current branch is `develop`.  
  
> (including trying define BOOST_MP_COMBA )  
  
We need to somehow pass `-DBOOST_MP_COMBA` to the compiler, so there are two cases:  
1. If you're compiling directly (using gcc/clang) just add `-DBOOST_MP_COMBA` to the compilation command.  
2. If you are using Boost.b2 then add `cxxflags="-DBOOST_MP_COMBA"` while invoking b2.  
  
> (Did you also mean to remove result.normalize() ..? )  
  
Yes, to avoid code duplication. Line 499 in new file takes care of it.

---

## Comment 3

> Username: pabristow  
> Created_at: 2020-04-01 08:49:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/206#issuecomment-607120497  

Sorry I haven't done this yet - suffering from StackOverflow.  Meanwhile I suggest you assume your fix is correct.

---
