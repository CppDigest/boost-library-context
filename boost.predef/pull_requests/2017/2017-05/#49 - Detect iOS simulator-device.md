# #49 Detect iOS simulator/device [Merged]

> Username: ruslo  
> Created at: 2017-05-19 20:19:15 UTC  
> Updated at: 2017-05-29 02:27:16 UTC  
> Merged at: 2017-05-29 02:20:00 UTC  
> Closed at: 2017-05-29 02:20:00 UTC  
> Url: https://github.com/boostorg/predef/pull/49  



---

## Review 1 [Commented]

> Username: grafikrobot  
> Created_at: 2017-05-21 21:48:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/predef/pull/49#pullrequestreview-39366488  

📁 include/boost/predef/platform/ios.h

```diff
  28 |+ #if BOOST_OS_IOS
  29 |+ // https://opensource.apple.com/source/CarbonHeaders/CarbonHeaders-18.1/TargetConditionals.h
  30 |+ # include <TargetConditionals.h>
```

> Username: grafikrobot  
> Created_at: 2017-05-21 21:48:11 UTC  
> Updated_at: 2017-05-22 09:29:45 UTC  
> Url: https://github.com/boostorg/predef/pull/49#discussion_r117643831  

Boost mandates "4" space indents.


---

## Review 2 [Commented]

> Username: grafikrobot  
> Created_at: 2017-05-21 21:50:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/predef/pull/49#pullrequestreview-39366543  

📁 include/boost/predef/platform/ios.h

```diff
  37 |+ # endif
  38 |+ #endif
  39 |+ 
```

> Username: grafikrobot  
> Created_at: 2017-05-21 21:50:48 UTC  
> Updated_at: 2017-05-22 09:29:45 UTC  
> Url: https://github.com/boostorg/predef/pull/49#discussion_r117643881  

Need to add logic for indicating the platform was detected. That avoids multiple detection. See here for example https://github.com/boostorg/predef/blob/develop/include/boost/predef/platform/windows_store.h#L33


---

## Review 3 [Commented]

> Username: grafikrobot  
> Created_at: 2017-05-21 21:52:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/predef/pull/49#pullrequestreview-39366589  

📁 include/boost/predef/platform/ios.h

```diff
  37 |+ # endif
  38 |+ #endif
  39 |+ 
```

> Username: grafikrobot  
> Created_at: 2017-05-21 21:52:11 UTC  
> Updated_at: 2017-05-22 09:29:45 UTC  
> Url: https://github.com/boostorg/predef/pull/49#discussion_r117643921  

Need to add def for text name pf platform. See example here https://github.com/boostorg/predef/blob/develop/include/boost/predef/platform/windows_store.h#L38


---

## Review 4 [Commented]

> Username: grafikrobot  
> Created_at: 2017-05-21 21:53:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/predef/pull/49#pullrequestreview-39366621  

📁 include/boost/predef/platform/ios.h

```diff
  38 |+ #endif
  39 |+ 
  40 |+ #endif // BOOST_PREDEF_PLAT_IOS_H
```

> Username: grafikrobot  
> Created_at: 2017-05-21 21:53:05 UTC  
> Updated_at: 2017-05-22 09:29:45 UTC  
> Url: https://github.com/boostorg/predef/pull/49#discussion_r117643950  

Need to add logic for inclusion in unit testing. See example here https://github.com/boostorg/predef/blob/develop/include/boost/predef/platform/windows_store.h#L42


---

## Comment 5

> Username: ruslo  
> Created_at: 2017-05-22 09:30:39 UTC  
> Url: https://github.com/boostorg/predef/pull/49#issuecomment-303048731  

> Boost mandates "4" space indents  
> Need to add logic for indicating the platform was detected  
> Need to add def for text name pf platform  
> Need to add logic for inclusion in unit testing  
  
Fixed

---
