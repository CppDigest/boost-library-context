# #57 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:24:03 UTC  
> Updated at: 2025-05-03 00:11:14 UTC  
> Merged at: 2025-05-03 00:11:14 UTC  
> Closed at: 2025-05-03 00:11:14 UTC  
> Url: https://github.com/boostorg/exception/pull/57  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:53:34 UTC  
> Url: https://github.com/boostorg/exception/pull/57#issuecomment-2295308610  

Please review and merge this PR at your earliest convenience.

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2025-05-02 16:08:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/exception/pull/57#pullrequestreview-2812393356  

📁 example/Jamfile

```diff
  13 | obj cloning_1 : cloning_1.cpp ;
  12 |- obj cloning_2 : cloning_2.cpp : <threading>multi ;
  14 |+ # obj cloning_2 : cloning_2.cpp /boost/thread//boost_thread : <threading>multi ;
```

> Username: pdimov  
> Created_at: 2025-05-02 16:08:40 UTC  
> Url: https://github.com/boostorg/exception/pull/57#discussion_r2071839614  

Why is this commented out?

> Username: grafikrobot  
> Created_at: 2025-05-02 16:30:12 UTC  
> Updated_at: 2025-05-02 16:30:13 UTC  
> Url: https://github.com/boostorg/exception/pull/57#discussion_r2071865026  

I don't honestly remember. It's been more than a year. But the surface answer is that the test itself doesn't work. Irrespective of the B2 changes.

> Username: grafikrobot  
> Created_at: 2025-05-02 23:29:35 UTC  
> Url: https://github.com/boostorg/exception/pull/57#discussion_r2072235756  

Uncommented now.


---
