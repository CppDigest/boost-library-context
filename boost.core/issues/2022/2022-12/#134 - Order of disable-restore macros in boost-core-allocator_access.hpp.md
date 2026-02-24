# #134 - Order of disable/restore macros in boost/core/allocator_access.hpp [Closed]

> Username: joaquintides  
> Created at: 2022-12-12 12:50:13 UTC  
> Updated at: 2022-12-15 13:35:07 UTC  
> Closed at: 2022-12-15 13:35:07 UTC  
> Url: https://github.com/boostorg/core/issues/134  

The aforementioned file has the following macros for disabling and then restoring warnings:  
```cpp  
#if defined(_LIBCPP_SUPPRESS_DEPRECATED_PUSH)  
_LIBCPP_SUPPRESS_DEPRECATED_PUSH  
#endif  
#if defined(_STL_DISABLE_DEPRECATED_WARNING)  
_STL_DISABLE_DEPRECATED_WARNING  
#endif  
#if defined(_MSC_VER)  
#pragma warning(push)  
#pragma warning(disable:4996)  
#endif  
  
...  
  
#if defined(_LIBCPP_SUPPRESS_DEPRECATED_POP)  
_LIBCPP_SUPPRESS_DEPRECATED_POP  
#endif  
#if defined(_STL_RESTORE_DEPRECATED_WARNING)  
_STL_RESTORE_DEPRECATED_WARNING  
#endif  
#if defined(_MSC_VER)  
#pragma warning(pop)  
#endif  
```  
Shouldn't the restore macros be written in reverse order, or else shouldn't the macros be applied in an exclusive manner by way of `#elif`s?

---

## Comment 1

> Username: glenfe  
> Created at: 2022-12-12 15:07:05 UTC  
> Url: https://github.com/boostorg/core/issues/134#issuecomment-1346667853  

Reverse order sounds better. I'll take care of it for 1.82.0

---

## Comment 2

> Username: joaquintides  
> Created at: 2022-12-14 09:14:08 UTC  
> Url: https://github.com/boostorg/core/issues/134#issuecomment-1350692662  

IMHO `#elif`s are better because `defined(_STL_RESTORE_DEPRECATED_WARNING)` and `defined(_MSC_VER)` are not mutually exclusive, so you end up silencing the same warning twice.

---

## Comment 3

> Username: glenfe  
> Created at: 2022-12-15 13:35:07 UTC  
> Url: https://github.com/boostorg/core/issues/134#issuecomment-1353075574  

Addressed in 2286749f977e003f3ad996fef6ee50a29d8b8841.
