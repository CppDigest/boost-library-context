# #10 Fix linker flags with clang, close #5820 [Closed]

> Username: Flast  
> Created at: 2014-05-01 14:22:05 UTC  
> Updated at: 2021-10-02 22:36:01 UTC  
> Closed at: 2014-06-17 06:05:34 UTC  
> Url: https://github.com/boostorg/build/pull/10  

Use --{start,end}-group to link objects in correct order. Also use -B{dynamic,static} switches to link static libraries as "static libraries".  
  
see also: https://svn.boost.org/trac/boost/ticket/5820

---

## Comment 1

> Username: vprus  
> Created_at: 2014-05-22 12:55:44 UTC  
> Url: https://github.com/boostorg/build/pull/10#issuecomment-43883713  

Could you make the commit summary a bit more descriptive? "Fix linker flags with clang" does not tell how it was broken. I assume the fix is fairly agnostic to clang version since a native system linker is used anyway?

---

## Comment 2

> Username: vprus  
> Created_at: 2014-06-17 06:05:34 UTC  
> Url: https://github.com/boostorg/build/pull/10#issuecomment-46270466  

I've merged this, thanks for the patch!

---

## Comment 3

> Username: Flast  
> Created_at: 2014-06-17 07:09:36 UTC  
> Url: https://github.com/boostorg/build/pull/10#issuecomment-46274206  

Ah! Sorry for forgetting the update notice.  
And thank you for merging.

---
