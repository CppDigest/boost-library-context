# #9 defined tuple_size/tuple_element as class [Closed]

> Username: bebuch  
> Created at: 2018-01-05 12:21:11 UTC  
> Updated at: 2018-01-05 13:19:49 UTC  
> Closed at: 2018-01-05 13:19:49 UTC  
> Url: https://github.com/boostorg/tuple/pull/9  

clang with libc++ does warn about struct, since it is declared as class in libc++.  
  
I found:  
https://groups.google.com/a/isocpp.org/forum/#!topic/std-discussion/QC-AMb5oO1w  
  
if I understand the thread correctly, MSVC also requires class, while GCC doesn't care.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-01-05 13:15:00 UTC  
> Url: https://github.com/boostorg/tuple/pull/9#issuecomment-355551935  

No, MSVC stdlib defines it as struct `14.0\VC\include\utility`:  
  
```cpp  
	// TEMPLATE STRUCT tuple_size  
template<class _Tuple>  
	struct tuple_size;  
```  
```cpp  
	// CLASS tuple_element (find element by index)  
template<size_t _Index,  
	class _Tuple>  
	struct tuple_element;  
  
```

---

## Comment 2

> Username: bebuch  
> Created_at: 2018-01-05 13:19:49 UTC  
> Url: https://github.com/boostorg/tuple/pull/9#issuecomment-355552843  

Okay, than the patch would make test incompatible to MS. Thanks a lot for checking!  
  
I hate Microsoft …

---
