# #542 Fix default ctor of homogeneous_color_base for reference pixel elements [Merged]

> Username: mloskot  
> Created at: 2021-01-21 22:17:35 UTC  
> Updated at: 2021-01-22 17:01:57 UTC  
> Merged at: 2021-01-22 17:01:54 UTC  
> Closed at: 2021-01-22 17:01:54 UTC  
> Url: https://github.com/boostorg/gil/pull/542  

### Description  
  
If `Element` is a reference, then Element v{} is ill-formed.  
  
<!-- What does this pull request do? -->  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- Refines #273 which aimed to correctly value-initialize channel and pixel value members  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s) - already in place  
- [x] Ensure all CI builds pass - Travis CI, R.I.P.   
- [x] Review and approve (via [#boost at Slack](https://cpplang.slack.com/archives/C27KZLB0X/p1611301724009500))

---
