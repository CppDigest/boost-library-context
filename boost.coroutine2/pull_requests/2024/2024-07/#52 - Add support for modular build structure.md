# #52 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:30:32 UTC  
> Updated at: 2024-08-28 10:37:12 UTC  
> Merged at: 2024-08-28 10:37:12 UTC  
> Closed at: 2024-08-28 10:37:12 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/52  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:01:35 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/52#issuecomment-2295310675  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: olk  
> Created_at: 2024-08-18 18:46:32 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/52#issuecomment-2295354375  

> Please review and merge this PR at your earliest convenience.  
  
All checks are failing ... ?!

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-08-18 18:59:21 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/52#issuecomment-2295357246  

Recycled to re-run the tests.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2024-08-18 19:04:24 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/52#issuecomment-2295358564  

The failures are unrelated to my changes AFAICT. Things build correctly elsewhere.. https://github.com/grafikrobot/boost-b2-modular/actions/runs/10432959230/job/28894198809 and https://github.com/grafikrobot/boost-b2-modular/actions/runs/10432959230/job/28894198183

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2024-08-24 02:56:04 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/52#issuecomment-2308009280  

The failures look the same as for Context. So you might want to apply similar changes here to what is done in this PR: https://github.com/boostorg/context/pull/257

---
