# #723 fix: Convolution in convolve_2d. [Merged]

> Username: cgringmuth  
> Created at: 2023-01-31 13:01:50 UTC  
> Updated at: 2023-03-31 07:54:49 UTC  
> Merged at: 2023-02-07 13:10:39 UTC  
> Closed at: 2023-02-07 13:10:39 UTC  
> Url: https://github.com/boostorg/gil/pull/723  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
This PR fixes the issue explained in #722.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2023-01-31 20:16:56 UTC  
> Url: https://github.com/boostorg/gil/pull/723#issuecomment-1411005053  

The fix looks good to me, https://en.wikipedia.org/wiki/Sobel_operator also agrees  
  
![image](https://wikimedia.org/api/rest_v1/media/math/render/svg/848abd56e0e33cf402f01183bfe1f68a93fb34a9)  
  
  
Since the previous values come from #392, I'd give @simmplecoder a chance to check the proposed fix.  
If none arrives and our tests are green, I'm happy to merge it.   
  
Thank you!

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2023-02-07 13:09:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/723#pullrequestreview-1287070454  

Thank you

---
