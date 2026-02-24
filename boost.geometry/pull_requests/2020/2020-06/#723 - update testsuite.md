# #723 [test] update testsuite [Merged]

> Username: barendgehrels  
> Created at: 2020-06-10 12:25:48 UTC  
> Updated at: 2020-10-15 23:42:10 UTC  
> Merged at: 2020-06-24 09:15:32 UTC  
> Closed at: 2020-06-24 09:15:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/723  

- some cases are fixed in the meantime, this is updated  
- in `difference` there might be slivers which are still OK, therefore an `count_set` is used there  
- when `BOOST_GEOMETRY_TEST_FAILURES` is defined, it also writes expected failures - this makes it much easier to detect improvements (or regressions)  
- this also shows insight in the current state, which is more or less in par with the rescaling - this means we might switch in next release (and one or two releases later remove the rescaling code)

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2020-06-15 13:05:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/723#pullrequestreview-430623316  

Thanks! Looks ok to me.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2020-06-24 09:16:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/723#issuecomment-648701500  

Merged, assuming no objections, and next PR is to built on top of this one.  
If there are still considerations, let me know.

---
