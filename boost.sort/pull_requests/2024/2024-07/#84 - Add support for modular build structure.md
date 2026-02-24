# #84 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:32:16 UTC  
> Updated at: 2024-08-20 11:10:30 UTC  
> Merged at: 2024-08-20 11:10:30 UTC  
> Closed at: 2024-08-20 11:10:30 UTC  
> Url: https://github.com/boostorg/sort/pull/84  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Changes requested]

> Username: spreadsort  
> Created_at: 2024-08-02 22:43:53 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/sort/pull/84#pullrequestreview-2216495160  

Please fix the test failures.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-02 22:49:56 UTC  
> Url: https://github.com/boostorg/sort/pull/84#issuecomment-2266232073  

> Please fix the test failures.  
  
I would love to. But 2/3 are this:  
  
```  
/__e/node20/bin/node: /lib/x86_64-linux-gnu/libm.so.6: version `GLIBC_2.27' not found (required by /__e/node20/bin/node)  
```  
  
And the Mac ones appear to be issues in the library.

---

## Comment 3

> Username: spreadsort  
> Created_at: 2024-08-03 12:29:47 UTC  
> Url: https://github.com/boostorg/sort/pull/84#issuecomment-2266697672  

Also, this pull request is marked as a draft.  If you want me to review it, please fix that.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2024-08-03 18:21:16 UTC  
> Url: https://github.com/boostorg/sort/pull/84#issuecomment-2267093488  

> Also, this pull request is marked as a draft. If you want me to review it, please fix that.  
  
To quote myself, from the PR description..  
  
> This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2024-08-18 16:03:40 UTC  
> Url: https://github.com/boostorg/sort/pull/84#issuecomment-2295311243  

Please review and merge this PR at your earliest convenience.

---
