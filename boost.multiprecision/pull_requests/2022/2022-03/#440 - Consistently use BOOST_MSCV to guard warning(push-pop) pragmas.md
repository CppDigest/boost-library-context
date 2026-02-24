# #440 Consistently use BOOST_MSCV to guard warning(push/pop) pragmas [Merged]

> Username: mariospr  
> Created at: 2022-03-06 09:53:01 UTC  
> Updated at: 2022-03-06 21:06:02 UTC  
> Merged at: 2022-03-06 18:42:37 UTC  
> Closed at: 2022-03-06 18:42:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/440  

There was some inconsistency as in some places _MSC_VER was used  
instead, plus there was a bug on cpp_int.hpp where BOOST_MSVC was  
used for warning(push) while _MSC_VER was used for warning(pop).

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-03-06 11:29:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/440#issuecomment-1059944845  

Thanks @mariospr , in an over-abundance of caution, I'll let the CI checks finish and then merge.

---

## Comment 2

> Username: mariospr  
> Created_at: 2022-03-06 21:06:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/440#issuecomment-1060039094  

Cool, thanks!

---
