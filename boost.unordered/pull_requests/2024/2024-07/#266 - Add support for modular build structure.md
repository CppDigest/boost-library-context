# #266 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:29:26 UTC  
> Updated at: 2024-09-11 17:44:32 UTC  
> Merged at: 2024-09-11 17:44:31 UTC  
> Closed at: 2024-09-11 17:44:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/266  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: k3DW  
> Created_at: 2024-07-20 22:53:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/266#pullrequestreview-2190073854  

📁 build.jam

```diff
   1 |+ # Copyright René Ferdinand Rivera Morell 2023
```

> Username: k3DW  
> Created_at: 2024-07-20 22:53:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/266#discussion_r1685547469  

I assume this should say `2024` instead of `2023`

> Username: grafikrobot  
> Created_at: 2024-07-20 22:55:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/266#discussion_r1685547726  

No entirely incorrect... Some of the changes have been around since last year. But, yes, I should update that.


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 16:00:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/266#issuecomment-2295310253  

Please review and merge this PR at your earliest convenience.

---
