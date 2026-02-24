# #489 Implemented mechanism to reverse kernel_2d [Merged]

> Username: lpranam  
> Created at: 2020-04-25 18:16:25 UTC  
> Updated at: 2021-12-30 01:45:54 UTC  
> Merged at: 2020-04-27 12:44:45 UTC  
> Closed at: 2020-04-27 12:44:45 UTC  
> Url: https://github.com/boostorg/gil/pull/489  

### Description  
  
Provided overloads to reverse `kernel_2d` and `kernel_2d_fixed`  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2020-04-25 18:39:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/489#pullrequestreview-400420264  

📁 test/extension/numeric/kernel.cpp

```diff
 178 |+     for (size_t i = 0; i < k.size() * k.size(); i++)
 179 |+     {
 180 |+         BOOST_TEST_EQ(d[i], k[(k.size()*k.size()) - 1 - i]);
```

> Username: mloskot  
> Created_at: 2020-04-25 18:39:15 UTC  
> Url: https://github.com/boostorg/gil/pull/489#discussion_r415118751  

Hopefully this does not trigger compiler warning

> Username: lpranam  
> Created_at: 2020-04-25 19:25:04 UTC  
> Url: https://github.com/boostorg/gil/pull/489#discussion_r415127003  

Maybe it will, i overlooked it. Will check and correct it if needed in the morning... :)

> Username: lpranam  
> Created_at: 2020-04-26 05:11:11 UTC  
> Url: https://github.com/boostorg/gil/pull/489#discussion_r415218448  

does not seem to trigger any compiler warning :)

> Username: mloskot  
> Created_at: 2020-04-27 10:44:04 UTC  
> Url: https://github.com/boostorg/gil/pull/489#discussion_r415705741  

👍


---
