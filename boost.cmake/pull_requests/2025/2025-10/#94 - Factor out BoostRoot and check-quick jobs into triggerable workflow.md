# #94 Factor out BoostRoot and check-quick jobs into triggerable workflow [Closed]

> Username: Flamefire  
> Created at: 2025-10-09 06:59:41 UTC  
> Updated at: 2025-10-10 12:12:25 UTC  
> Closed at: 2025-10-10 11:53:15 UTC  
> Url: https://github.com/boostorg/cmake/pull/94  

This allows manually running the workflow e.g. to test recent changes in the main Boost project/libraries.  
  
  
The idea is that library authors can request to run these 2 jobs after merging to develop/master to check it works in general and across different CMake versions

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-09 13:32:51 UTC  
> Url: https://github.com/boostorg/cmake/pull/94#issuecomment-3385916820  

How would library authors request that?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-10 08:16:19 UTC  
> Url: https://github.com/boostorg/cmake/pull/94#issuecomment-3388792568  

Via Slack or ML. I figured here is the best way to test things CMake.  
  
Putting it into boostorg/boost instead to run on every submodule update commit might be too much, but possibly better as more complete/immediate. What do you think?

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-10-10 11:53:15 UTC  
> Url: https://github.com/boostorg/cmake/pull/94#issuecomment-3389689007  

I split the workflows in a slightly different way.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-10-10 12:04:21 UTC  
> Url: https://github.com/boostorg/cmake/pull/94#issuecomment-3389730304  

[So this](https://github.com/boostorg/cmake/commit/d9c76a4ae81b185f92cb5529b446c99f4c45b307#diff-f8c8b00e55f2319276ed6291ab7bdd8a8609510e2267aa21c5e71871af69040d)  
  
4 times the jobs to 457, MS will love us ;-)  
  
What's missing is the workflow trigger for the cmake_versions.yml  
  
I really think it makes sense to be able to trigger running just that without making a commit here e.g. when checking a change from a library for whether it works for all CMake versions or when it does fail. Or before releases. At least the tags should trigger a run so we notice it for RCs at least when they are tagged. With the trigger we can run it for the beta before that.

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-10-10 12:12:25 UTC  
> Url: https://github.com/boostorg/cmake/pull/94#issuecomment-3389763199  

RCs aren't tagged, but the betas are, so it probably makes sense to make CI run on tags.  
  
You can add the workflow-dispatch in subsequent PRs if you like.

---
