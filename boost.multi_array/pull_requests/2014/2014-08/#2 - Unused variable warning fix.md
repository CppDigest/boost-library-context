# #2 Unused variable warning fix [Closed]

> Username: sergiud  
> Created at: 2014-08-04 12:57:24 UTC  
> Updated at: 2014-08-19 11:58:44 UTC  
> Closed at: 2014-08-19 11:58:32 UTC  
> Url: https://github.com/boostorg/multi_array/pull/2  

This change enables clean compilation against the library using MSVC 12.0 at level 4 warnings. The warning is issued in release mode only.

---

## Comment 1

> Username: rxg  
> Created_at: 2014-08-14 23:03:13 UTC  
> Url: https://github.com/boostorg/multi_array/pull/2#discussion_r16272406  

If you replace:  
  
(void)bound_adjustment;  
  
with  
  
ignore_unused_variable_warning(bound_adjustment);  
  
does MSVC 12.0 at level 4 still issue a warning in release mode?

---

## Comment 2

> Username: sergiud  
> Created_at: 2014-08-19 11:58:32 UTC  
> Url: https://github.com/boostorg/multi_array/pull/2#issuecomment-52623120  

Yes, it does fix the problem. Thanks.

---
