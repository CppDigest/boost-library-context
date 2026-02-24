# #180 - "may be used uninitialized" warning in extract.hpp [Open]

> Username: jakobandersen  
> Created at: 2017-12-15 14:17:07 UTC  
> Updated at: 2024-05-14 06:37:49 UTC  
> Url: https://github.com/boostorg/python/issues/180  

With GCC 7.2 on Fedora (g++ --version: g++ (GCC) 7.2.1 20170915 (Red Hat 7.2.1-2)) I get the following (probably false) warning:  
```  
/include/boost/python/extract.hpp:185:11: warning: ‘*((void*)&<anonymous> +24)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
```  
It would be nice to not get it :-) (without using `-Wno-maybe-uninitialized`)

---

## Comment 1

> Username: ASxa86  
> Created at: 2018-06-11 16:32:53 UTC  
> Updated at: 2018-06-11 16:33:30 UTC  
> Url: https://github.com/boostorg/python/issues/180#issuecomment-396304886  

I'm having this same issue. GCC 7.3 on CentOS 7. I'm not noticing this issue when build in Debug.  
Boost Version: 1.67

---

## Comment 2

> Username: Algomorph  
> Created at: 2019-03-12 21:32:57 UTC  
> Updated at: 2019-03-12 21:33:11 UTC  
> Url: https://github.com/boostorg/python/issues/180#issuecomment-472189894  

Same here.  
GCC 7.3.0  
Ubuntu 18.04.  
Boost 1.68

---

## Comment 3

> Username: jefftrull  
> Created at: 2021-04-04 20:51:54 UTC  
> Url: https://github.com/boostorg/python/issues/180#issuecomment-813097482  

This pops up frequently in [graph-tool](https://graph-tool.skewed.de/); would be great to understand it.

---

## Comment 4

> Username: jefftrull  
> Created at: 2021-04-23 05:12:32 UTC  
> Url: https://github.com/boostorg/python/issues/180#issuecomment-825390862  

FWIW for me, this problem, present through gcc 9.3, goes away with 10.1.

---

## Comment 5

> Username: Evander83  
> Created at: 2024-05-14 06:37:48 UTC  
> Url: https://github.com/boostorg/python/issues/180#issuecomment-2109396111  

Use check() before assign. It works for me.  
int value = extract<int>(py_obj).check() ? int(extract<int>(py_obj)) : 0;
