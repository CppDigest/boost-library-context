# #63 - Use of this header (ice_eq.hpp) is deprecated [Closed]

> Username: andreasbaumann  
> Created at: 2016-04-23 07:41:29 UTC  
> Updated at: 2016-06-21 11:16:05 UTC  
> Closed at: 2016-06-18 10:27:35 UTC  
> Url: https://github.com/boostorg/python/issues/63  

Compiling on ArchLinux I get:  
  
```  
In file included from /usr/include/boost/type_traits/ice.hpp:15:0,  
                 from /usr/include/boost/python/detail/def_helper.hpp:9,  
                 from /usr/include/boost/python/class.hpp:29,  
                 from /usr/include/boost/python.hpp:18,  
                 from /home/abaumann/strusBindings/lang/python/strusPythonModule.cpp:10:  
/usr/include/boost/type_traits/detail/ice_or.hpp:17:71: note: #pragma message: NOTE: Use of this header (ice_or.hpp) is deprecated  
 # pragma message("NOTE: Use of this header (ice_or.hpp) is deprecated")  
```

---

## Comment 1

> Username: bryanfok  
> Created at: 2016-06-09 03:30:01 UTC  
> Url: https://github.com/boostorg/python/issues/63#issuecomment-224791835  

having the same problem

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-06-18 10:27:35 UTC  
> Url: https://github.com/boostorg/python/issues/63#issuecomment-226934447  

This has been fixed for over a year (and thus at least two releases) (see https://github.com/boostorg/python/commit/0a4c76b9ac16974d7d4f164cf6179095217a981a#diff-324236aa32705c0aab7f5e6d8c8a0230), but it will take some time to percolate through to the various GNU/Linux distros.

---

## Comment 3

> Username: count0  
> Created at: 2016-06-18 10:30:21 UTC  
> Url: https://github.com/boostorg/python/issues/63#issuecomment-226934530  

I don't think that is right. I see this problem with boost 1.60, which is less than two releases ago.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2016-06-18 10:37:58 UTC  
> Url: https://github.com/boostorg/python/issues/63#issuecomment-226934793  

You are right, it looks (https://github.com/boostorg/python/commit/0a4c76b9ac16974d7d4f164cf6179095217a981a) as if this was only merged into master after the 1.60 release. So only the last release has the fix.

---

## Comment 5

> Username: andreasbaumann  
> Created at: 2016-06-21 11:16:04 UTC  
> Url: https://github.com/boostorg/python/issues/63#issuecomment-227410344  

Ah. Thanks for checking. So the warning is rather harmless.
