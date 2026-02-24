# #41 Add cmake compatibility [Closed]

> Username: markovchainy  
> Created at: 2018-03-14 19:11:42 UTC  
> Updated at: 2018-04-06 09:17:28 UTC  
> Closed at: 2018-03-14 19:59:18 UTC  
> Url: https://github.com/boostorg/iterator/pull/41  

This allows the boost/iterator library to be used as an external module in a CMake-based project.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-03-14 19:26:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/41#issuecomment-373145768  

I would like to see a common approach for supporting CMake in Boost libraries. AFAIR, there is at least one solution pending in the review queue.

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-03-14 19:27:28 UTC  
> Url: https://github.com/boostorg/iterator/pull/41#issuecomment-373146002  

Also, we don't merge PRs to master.

---

## Comment 3

> Username: markovchainy  
> Created_at: 2018-03-14 19:28:26 UTC  
> Url: https://github.com/boostorg/iterator/pull/41#issuecomment-373146264  

Is that for another repo? I can only see this pull request on Iterator.  
Happy to follow a standard approach if you can point me at an example.

---

## Comment 4

> Username: Lastique  
> Created_at: 2018-03-14 19:47:33 UTC  
> Url: https://github.com/boostorg/iterator/pull/41#issuecomment-373151713  

No, I was talking about Boost review queue:  
  
http://www.boost.org/community/review_schedule.html  
  
Now that I look there, I can't find the proposal for adding CMake support. Maybe it has not been formally submitted yet. Still, I would rather not merge this and wait until the common solution has passed the review.

---

## Comment 5

> Username: Lastique  
> Created_at: 2018-03-14 19:59:18 UTC  
> Url: https://github.com/boostorg/iterator/pull/41#issuecomment-373155104  

For the reference, I was thinking about Boost.BCM:  
  
https://github.com/boost-cmake/bcm  
  
I'm going to close this PR now since it looks like a stray approach and not based on BCM or any other common tool that can be applied to all Boost libraries. Also, the PR is against master.

---
