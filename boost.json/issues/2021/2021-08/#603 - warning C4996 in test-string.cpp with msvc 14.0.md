# #603 - warning C4996 in test/string.cpp with msvc 14.0 [Closed]

> Username: grisumbras  
> Created at: 2021-08-07 14:08:37 UTC  
> Updated at: 2022-11-16 14:38:37 UTC  
> Closed at: 2022-11-16 14:38:37 UTC  
> Url: https://github.com/boostorg/json/issues/603  

https://www.boost.org/development/tests/master/output/teeks99-09-v14m-64onAMD64-json-msvc-14-0-warnings.html#string  
  
```  
string  
  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\xutility(2372): warning C4996: 'std::copy::_Unchecked_iterators::_Deprecate': Call to 'std::copy' with parameters that may be unsafe - this call relies on the caller to check that the passed values are correct. To disable this warning, use -D_SCL_SECURE_NO_WARNINGS. See documentation on how to use Visual C++ 'Checked Iterators'  
```  
  
Judging by https://docs.microsoft.com/en-us/cpp/standard-library/checked-iterators?view=msvc-140 this is due to using pointers with `std::copy`. Should be possible to remove this warning via `#define _ITERATOR_DEBUG_LEVEL 0` around the call to `std::copy`.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-11-16 14:38:36 UTC  
> Url: https://github.com/boostorg/json/issues/603#issuecomment-1317124301  

Fixed in 77c05d203148f67fecbb261f3e856207e9614b51
