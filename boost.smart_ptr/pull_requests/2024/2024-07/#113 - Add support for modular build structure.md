# #113 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:23:05 UTC  
> Updated at: 2024-08-27 18:51:40 UTC  
> Merged at: 2024-08-27 18:51:39 UTC  
> Closed at: 2024-08-27 18:51:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-08-17 15:37:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2294893731  

What's the problem with libs/functional here?

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-17 16:33:11 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2294906995  

> What's the problem with libs/functional here?  
  
The problem is that this: https://github.com/boostorg/smart_ptr/actions/runs/10429524218/job/28887047180?pr=113#step:7:18 does not install functional. Which is needed for some of the tests.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-08-17 16:35:10 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2294907407  

Why do you think it's needed?

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2024-08-17 16:47:49 UTC  
> Updated_at: 2024-08-17 16:48:59 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2294910462  

> Why do you think it's needed?  
  
- https://github.com/boostorg/smart_ptr/blob/develop/test/ip_hash_test.cpp#L12  
- https://github.com/boostorg/smart_ptr/blob/develop/test/sp_hash_test.cpp#L12  
- https://github.com/boostorg/smart_ptr/blob/develop/test/sp_hash_test2.cpp#L12  
- https://github.com/boostorg/smart_ptr/blob/develop/test/wp_hash_test.cpp#L7  
- https://github.com/boostorg/smart_ptr/blob/develop/test/sp_hash_test3.cpp#L12

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-08-17 16:50:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2294910987  

This header isn't in libs/functional.

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2024-08-17 16:54:47 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2294912069  

> This header isn't in libs/functional.  
  
Seriously :-( After searching I see it's container_hash. So messy for the brain. I'll fix it. And the other places where this happens.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2024-08-18 15:52:25 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2295308302  

Please review and merge this PR at your earliest convenience.

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-08-18 16:14:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2295314473  

This still contains a lot of unnecessary CI changes.

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2024-08-18 16:17:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2295315165  

> This still contains a lot of unnecessary CI changes.  
  
Grr.. I thought I had fixed that.

---

## Comment 10

> Username: grafikrobot  
> Created_at: 2024-08-18 16:22:37 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2295316624  

@pdimov okay, removed them. Dealing with >150 PRs has seriously jumbled my brain at this point. :-(

---

## Comment 11

> Username: pdimov  
> Created_at: 2024-08-18 16:46:29 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/113#issuecomment-2295323089  

Good thing I only need to deal with ~23 of these or so.

---
