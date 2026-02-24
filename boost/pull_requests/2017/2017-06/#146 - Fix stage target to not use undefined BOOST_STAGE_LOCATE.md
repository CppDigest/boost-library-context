# #146 Fix stage target to not use undefined BOOST_STAGE_LOCATE [Merged]

> Username: pdimov  
> Created at: 2017-06-30 17:12:54 UTC  
> Updated at: 2017-12-17 01:51:54 UTC  
> Merged at: 2017-12-17 01:51:39 UTC  
> Closed at: 2017-12-17 01:51:39 UTC  
> Url: https://github.com/boostorg/boost/pull/146  

`BOOST_STAGE_LOCATE` is undefined in `boost-install`, so it always uses the target name, `stage`. This means that when `b2 stage` is invoked from, f.ex. `libs/system/build`, a local `libs/system/build/stage` directory is used for staging. This is almost certainly wrong. In addition, `BOOST_STAGE_LOCATE` in `boostcpp` is by default the relative path `stage`, so even if used with `modules.peek`, it works the same way.  
  
I'm not entirely sure that the proposed replacement is correct in all use cases, but it's an improvement. :-)

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-10-14 15:35:59 UTC  
> Url: https://github.com/boostorg/boost/pull/146#issuecomment-336642839  

Opinion on this @vprus? `BOOST_STAGE_LOCATE` is used undefined, this can't be correct?

---
