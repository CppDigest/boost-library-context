# #181 - Warnings in enum.cpp [Closed]

> Username: pdimov  
> Created at: 2017-12-17 16:59:03 UTC  
> Updated at: 2017-12-18 20:26:10 UTC  
> Closed at: 2017-12-18 20:26:10 UTC  
> Url: https://github.com/boostorg/python/issues/181  

It looks like the compiler has a point here:  
```  
libs\python\src\object\enum.cpp(47) : warning C4190: 'auto_free' has C-linkage specified, but returns UDT 'boost::python::api::object' which is incompatible with C  
        C:\projects\boost-root\boost/python/object_core.hpp(238) : see declaration of 'boost::python::api::object'  
libs\python\src\object\enum.cpp(47) : warning C4930: 'boost::python::api::object auto_free(boost::python::handle<>)': prototyped function not called (was a variable definition intended?)  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-12-18 19:39:18 UTC  
> Url: https://github.com/boostorg/python/issues/181#issuecomment-352535816  

Huh, that's tricky. The compiler in question interprets that line as declaring a function, while what this ought to be is an object definition. So, the warning really hints at a memory leak (as the intent clearly is to create an object that will clean up the `mod` object at end-of-scope).  
  
It seems this line needs to be rewritten, perhaps as "object auto_free = object(handle<>(mod));" ?  
I'll work on a patch...

---

## Comment 2

> Username: pdimov  
> Created at: 2017-12-18 19:43:50 UTC  
> Url: https://github.com/boostorg/python/issues/181#issuecomment-352536962  

All compilers interpret it that way, but not all of them warn. clang 5, for instance, warns too.  
  
https://travis-ci.org/boostorg/check_build/jobs/317893921#L2311  
  
The easiest fix is to parenthesize it twice, `object auto_free((handle<>(mod)));`.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-12-18 19:45:05 UTC  
> Url: https://github.com/boostorg/python/issues/181#issuecomment-352537266  

OK, perhaps, but that spelling looks a bit obscure to me, so I prefer the assignment-operator syntax.

---

## Comment 4

> Username: pdimov  
> Created at: 2017-12-18 19:46:44 UTC  
> Url: https://github.com/boostorg/python/issues/181#issuecomment-352537683  

Looks like it could result in an extra copy, although I'm not sure how relevant is this in context.

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2017-12-18 19:49:09 UTC  
> Url: https://github.com/boostorg/python/issues/181#issuecomment-352538344  

Nah, the `object a = object(...)` syntax is just a different spelling. There is no extra temporary object involved, at least according to the standard. I can't speak to compiler conformance in general, of course. :-)

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-12-18 20:05:59 UTC  
> Url: https://github.com/boostorg/python/issues/181#issuecomment-352542867  

Here is the fix: https://github.com/boostorg/python/commit/d6d54ce483c27ef2cddb4d43e6e775c5c496ec62. Let's close this once the warning is gone from the appropriate test runs.

---

## Comment 7

> Username: pdimov  
> Created at: 2017-12-18 20:24:58 UTC  
> Url: https://github.com/boostorg/python/issues/181#issuecomment-352547529  

I can confirm that the various MSVCs no longer issue the warning after the fix, in my local tests.

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2017-12-18 20:26:10 UTC  
> Url: https://github.com/boostorg/python/issues/181#issuecomment-352547788  

Excellent, thanks.
