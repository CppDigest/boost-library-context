# #390 fixed incorrect callbacks to finish_edge in undirected_dfs [Merged]

> Username: danielyxyang  
> Created at: 2024-10-08 15:02:17 UTC  
> Updated at: 2024-10-30 22:10:41 UTC  
> Merged at: 2024-10-30 06:47:54 UTC  
> Closed at: 2024-10-30 06:47:54 UTC  
> Url: https://github.com/boostorg/graph/pull/390  

Resolves #389

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2024-10-09 03:24:55 UTC  
> Url: https://github.com/boostorg/graph/pull/390#issuecomment-2401193378  

Not sure why all those Drone checks failed, but you probably should update to the tip of `develop` anyway because I have fixed some CI configuration.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2024-10-09 03:25:44 UTC  
> Url: https://github.com/boostorg/graph/pull/390#issuecomment-2401193968  

Please add a unit test that would fail on `develop` as it is but passes with your proposed change.

---

## Review 3 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-10-09 03:31:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/390#pullrequestreview-2355876603  

📁 include/boost/graph/undirected_dfs.hpp

```diff
  81 |                     stack.push_back(std::make_pair(u,
  83 |-                         std::make_pair(src_e, std::make_pair(++ei, ei_end))));
  82 |+                         std::make_pair(src_e, std::make_pair(ei+1, ei_end))));
```

> Username: jeremy-murphy  
> Created_at: 2024-10-09 03:31:07 UTC  
> Url: https://github.com/boostorg/graph/pull/390#discussion_r1792736411  

Probably has to be this so that it works with edge iterators that aren't random access.  
```diff  
-                        std::make_pair(src_e, std::make_pair(ei+1, ei_end))));  
+                        std::make_pair(src_e, std::make_pair(std::next(ei), ei_end))));  
```  
And, call me fussy, but I prefer this style anyway.

> Username: danielyxyang  
> Created_at: 2024-10-09 07:31:27 UTC  
> Url: https://github.com/boostorg/graph/pull/390#discussion_r1793012868  

Ah yes, makes sense! I'm not very fluent in C++, so thanks for pointing this out :)


---

## Comment 4

> Username: danielyxyang  
> Created_at: 2024-10-09 07:38:32 UTC  
> Url: https://github.com/boostorg/graph/pull/390#issuecomment-2401569594  

I added a unit test now based on the minimal working example in #389. I tested the test hehe and it passes with the changes I made and fails without these changes.  
  
Disclaimer: I know my coding style is quite different from what mostly prevails in this codebase. So feel free to adapt anything you think is important for maintaining consistency. Similarly, as C++ is not my main coding language, I'm sure there are quite a few things someone with more experience would have done better or differently. So feel free to make changes in this regard too :) Not sure though how the workflow here on Github is and whether you need write access to my fork to be able to make edits...

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2024-10-24 22:34:35 UTC  
> Updated_at: 2024-10-24 22:36:46 UTC  
> Url: https://github.com/boostorg/graph/pull/390#issuecomment-2436456785  

Huh, that's weird, the GitHub actions CI doesn't seem to be running, and the Drone CI ran instantly.  :|  
Sorry, I'll have to investigate what's going on with the CI before I can merge any PRs.  
PS. Oh, weird, GitHub said that "all checks passed" and then _later_ started the GitHub actions CI.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2024-10-30 06:48:34 UTC  
> Url: https://github.com/boostorg/graph/pull/390#issuecomment-2446000259  

Thanks again!

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2024-10-30 22:10:40 UTC  
> Url: https://github.com/boostorg/graph/pull/390#issuecomment-2448546818  

I was happy with the implementation, so I merged it, but actually the test could do with some improvement if you have time.  
Using a string as the output type works, but it's a little fragile. What would be better is a scoped enum, but since the output could be a vertex or an edge, it's not simple how to represent it. I would lean towards a sum type, but I'm open to alternative suggestions. Feel free to discuss ideas before you implement, if you're willing to take it on.   
I was also trying to think of more tests to add, and so far the only slightly meaningful test case I can think of is a V-shaped graph with three vertices. If the search starts from the obvious root, then the test will confirm that one branch is finished before the other one is started. It's not really related to the original bug anymore, it's just an edge case that I thought is worth having a test for.

---
