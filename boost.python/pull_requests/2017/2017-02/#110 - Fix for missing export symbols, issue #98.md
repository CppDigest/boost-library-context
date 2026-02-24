# #110 Fix for missing export symbols, issue #98 [Merged]

> Username: SPKorhonen  
> Created at: 2017-02-15 16:09:59 UTC  
> Updated at: 2017-02-15 16:53:29 UTC  
> Merged at: 2017-02-15 16:53:28 UTC  
> Closed at: 2017-02-15 16:53:29 UTC  
> Url: https://github.com/boostorg/python/pull/110  

Fix for missing export symbols in shared library of boost::python::numpy as reported and discussed in issue #98. Added config.hpp modified from python/detail/config.hpp for also numpy. Also added export declaration to functions/classes,

---

## Review 1 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2017-02-15 16:18:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/python/pull/110#pullrequestreview-22040829  

Thanks for the quick patch !  
I don't quite understand all the complexity in the new numpy/config.hpp header. Can you explain a bit ?  
Notably, what is all the first block starting with #ifdef BOOST_NO_OPERATORS_IN_NAMESPACE about ? Could you be more specific about what bug this is addressing ? But none of those macros appear to be used anywhere here, so perhaps this entire block could be removed ? It's not part of the dllexport / dllimport business anyhow, so should likely be part of a separate PR, if it's really needed.  
  
Thanks,

---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2017-02-15 16:26:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/110#pullrequestreview-22043140  

📁 include/boost/python/numpy/config.hpp

```diff
  14 |+ 
  15 |+ # include <boost/config.hpp>
  16 |+ # include <boost/detail/workaround.hpp>
```

> Username: stefanseefeld  
> Created_at: 2017-02-15 16:26:30 UTC  
> Updated_at: 2017-02-15 16:49:23 UTC  
> Url: https://github.com/boostorg/python/pull/110#discussion_r101318649  

What is this header needed for ?

> Username: SPKorhonen  
> Created_at: 2017-02-15 16:33:55 UTC  
> Updated_at: 2017-02-15 16:49:23 UTC  
> Url: https://github.com/boostorg/python/pull/110#discussion_r101320604  

It is not used anymore. I just tested it on my build rig. Submitted new version.


---

## Review 3 [Approved]

> Username: stefanseefeld  
> Created_at: 2017-02-15 16:51:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/python/pull/110#pullrequestreview-22050036  

OK, now this looks fine. Thanks a lot !

---

## Comment 4

> Username: SPKorhonen  
> Created_at: 2017-02-15 16:52:45 UTC  
> Url: https://github.com/boostorg/python/pull/110#issuecomment-280067869  

You're welcome! Good to have this fixed.   
  
PS. Sorry for the extra steps on the way!

---
