# #1 Switch visibility support to  BOOST_SYMBOL_EXPORT. Refs #2114 [Merged]

> Username: jhunold  
> Created at: 2014-01-04 10:13:42 UTC  
> Updated at: 2017-08-22 14:10:15 UTC  
> Merged at: 2017-02-13 15:43:24 UTC  
> Closed at: 2017-02-13 15:43:24 UTC  
> Url: https://github.com/boostorg/python/pull/1  

Found while cleaning up my git svn mirror after the conversion. The patch switches Boost.Python to the common BOOST_SYMBOL_\*  macros from Boost.Config. This includes some minor cleanups for gcc which does not like visibility attributes on forward declarations. Testest with gcc and msvc-9.0

---

## Comment 1

> Username: hotgloupi  
> Created_at: 2014-11-04 08:17:01 UTC  
> Url: https://github.com/boostorg/python/pull/1#issuecomment-61606555  

This patch sounds great !  
  
The only thing I don't know is about exception visibility, I always expose them on windows, and that's why I use in my projects a special macros that expand to correct visibility on any platform but windows.

---

## Review 2 [Commented]

> Username: lukaszforynski  
> Created_at: 2017-08-22 14:10:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/1#pullrequestreview-57789629  

📁 include/boost/python/object/instance.hpp

```diff
  12 | namespace boost { namespace python
  13 | {
  14 |-   struct BOOST_PYTHON_DECL_FORWARD instance_holder;
```

> Username: lukaszforynski  
> Created_at: 2017-08-22 14:10:15 UTC  
> Url: https://github.com/boostorg/python/pull/1#discussion_r134492025  

this is the offending line that is a cause of #131


---
