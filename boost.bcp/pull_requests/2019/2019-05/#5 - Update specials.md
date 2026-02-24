# #5 Update specials [Merged]

> Username: pdimov  
> Created at: 2019-05-26 17:36:07 UTC  
> Updated at: 2019-11-02 11:19:58 UTC  
> Merged at: 2019-10-31 19:30:27 UTC  
> Closed at: 2019-10-31 19:30:28 UTC  
> Url: https://github.com/boostorg/bcp/pull/5  

This updates `specials` to reflect the new `build` dependencies on `libs/headers` and `tools/boost_install`, and the dependency of `libs/test/build` on `libs/predef/check`.  
  
Unfortunately, after these fixes, everything is copied correctly, but `b2` then crashes (https://travis-ci.com/boostorg/bcp/jobs/203138557#L5404). This has something to do with `test` using `predef`. No idea what's wrong there. Perhaps @grafikrobot or @swatanabe can look into that.

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-06-17 12:04:14 UTC  
> Url: https://github.com/boostorg/bcp/pull/5#issuecomment-502654649  

John?

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-10-29 19:21:07 UTC  
> Updated_at: 2019-10-29 19:23:24 UTC  
> Url: https://github.com/boostorg/bcp/pull/5#issuecomment-547588182  

Ping? A user (on the C++ Slack) has just run into https://github.com/boostorg/boost/issues/282.

---
