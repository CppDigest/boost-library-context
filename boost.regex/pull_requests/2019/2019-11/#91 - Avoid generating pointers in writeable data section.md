# #91 Avoid generating pointers in writeable data section. [Merged]

> Username: rdoeffinger  
> Created at: 2019-11-05 21:25:06 UTC  
> Updated at: 2020-01-07 19:00:53 UTC  
> Merged at: 2020-01-07 19:00:53 UTC  
> Closed at: 2020-01-07 19:00:53 UTC  
> Url: https://github.com/boostorg/regex/pull/91  

They increase memory consumption and make exploits  
easier and are completely unnecessary.  
Avoid them by either avoiding the pointer indirection  
completely by using char arrays for strings instead  
of char pointers, convert "static" pointer variables  
to simple local variables, or mark the array of  
pointers as const instead of just the things pointed to.

---

## Comment 1

> Username: rdoeffinger  
> Created_at: 2019-11-08 09:54:59 UTC  
> Url: https://github.com/boostorg/regex/pull/91#issuecomment-551487312  

MinGW seems to have some issues:  
error: external linkage required for symbol 'boost::re_detail_107200::def_coll_names' because of '__dllexport__' attribute  
 BOOST_REGEX_DECL const char* const def_coll_names[] = {  
                                                   ^  
Have not yet figured out what the error is trying to tell me. VS2017 seems to be fine with the code, as well as Linux gcc...

---

## Comment 2

> Username: rdoeffinger  
> Created_at: 2019-11-08 09:59:44 UTC  
> Url: https://github.com/boostorg/regex/pull/91#issuecomment-551494241  

Why is def_coll_names marked as BOOST_REGEX_DECL  instead of "static"?  
I don't see it used outside the compilation unit...

---

## Comment 3

> Username: rdoeffinger  
> Created_at: 2019-11-16 17:41:04 UTC  
> Url: https://github.com/boostorg/regex/pull/91#issuecomment-554658323  

So just adding "extern" seems to fix the mingw problem.  
But see https://github.com/boostorg/regex/pull/93 for the alternative of simply not exposing them at all which seems better to me.

---
