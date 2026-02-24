# #123 Fix warnings about narrowing conversions on 64-bit systems with 32-bit int [Merged]

> Username: mkurdej  
> Created at: 2014-08-06 10:03:22 UTC  
> Updated at: 2014-08-06 11:14:04 UTC  
> Merged at: 2014-08-06 10:26:47 UTC  
> Closed at: 2014-08-06 10:26:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/123  

I have fixed some warnings appearing on (at least) MSVC12 64-bit, since int was used as difference type / size type, hence compiler warned about narrowing conversion (possible loss of data C4244).  
  
Everything builds fine and tests pass on MSVC12 64-bit.  
  
Still, there are lots of C4267 warnings about conversion from 'size_t' to 'int'

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-08-06 11:13:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/123#issuecomment-51321356  

The fixes did not include the `typename` keyword.  
g++-4.8 complained about it (and I am sure other compilers that I did not test with will).  
  
See PR #125 for the fix to the fix :-)

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-08-06 11:14:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/123#issuecomment-51321433  

Sure, thanks

---
