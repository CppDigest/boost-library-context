# #71 MIPS o32 Fixes [Merged]

> Username: jcowgill  
> Created at: 2018-01-11 14:40:12 UTC  
> Updated at: 2018-02-05 14:47:10 UTC  
> Merged at: 2018-01-16 04:44:41 UTC  
> Closed at: 2018-01-16 04:44:41 UTC  
> Url: https://github.com/boostorg/context/pull/71  

This PR contains a bunch of MIPS o32 fixes which allows the "fc" testsuite to pass on MIPS. I originally noticed these bugs when building pdns-resolver (which uses boost context) on Debian and pdns-resolver's testsuite segfaulting within boost context code.  
  
More details are in the commit messages.

---

## Comment 1

> Username: olk  
> Created_at: 2018-01-16 04:44:44 UTC  
> Url: https://github.com/boostorg/context/pull/71#issuecomment-357852534  

ty

---

## Comment 2

> Username: larsxschneider  
> Created_at: 2018-02-05 14:24:55 UTC  
> Url: https://github.com/boostorg/context/pull/71#issuecomment-363099035  

@olk Looks like you merged this PR as 9db1604 into `develop`. Later on someone (you?) rebased `develop` and removed the merge commit 9db1604. I pulled 9db1604 when it was still available and now my automation screams about the missing commit in the upstream repo. I assume all this was done intentional by you. If this is not the case, then there is a slight chance a bad actor is involved.  
  
/cc @pluehne

---

## Comment 3

> Username: olk  
> Created_at: 2018-02-05 14:47:10 UTC  
> Url: https://github.com/boostorg/context/pull/71#issuecomment-363105598  

9db1604 is a merge-commit - all relevant fixes are stilll contained in the git-history  
you could reset 15 commits back and then pull the git -history again  
```  
git reset --hard HEAD~15  
git pull  
```

---
