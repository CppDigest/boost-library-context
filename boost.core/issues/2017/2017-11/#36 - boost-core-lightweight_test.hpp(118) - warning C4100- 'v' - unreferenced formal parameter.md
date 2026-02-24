# #36 - boost/core/lightweight_test.hpp(118) : warning C4100: 'v' : unreferenced formal parameter [Closed]

> Username: jeking3  
> Created at: 2017-11-01 12:42:25 UTC  
> Updated at: 2017-11-01 18:08:05 UTC  
> Closed at: 2017-11-01 18:08:05 UTC  
> Url: https://github.com/boostorg/core/issues/36  

See matrix build http://www.boost.org/development/tests/develop/output/teeks99-09-c-win2012R2-64on64-uuid-msvc-10-0-warnings.html#test_uuid  
  
This warning appears many times:  
  
D:\teeks99-09\run\boost_root\boost/core/lightweight_test.hpp(118) : warning C4100: 'v' : unreferenced formal parameter  
  
Seems to be happening on older msvc compilers, before 12.0.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-11-01 13:09:21 UTC  
> Url: https://github.com/boostorg/core/issues/36#issuecomment-341101470  

```  
inline const void* test_output_impl(std::nullptr_t v) { return v; }  
```  
  
Gotta love msvc-10.0.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-11-01 13:40:05 UTC  
> Url: https://github.com/boostorg/core/issues/36#issuecomment-341108681  

Should be fixed in https://github.com/boostorg/core/commit/2a3387451fedc1222e9f43e24d300a155295f20d.

---

## Comment 3

> Username: jeking3  
> Created at: 2017-11-01 18:08:05 UTC  
> Url: https://github.com/boostorg/core/issues/36#issuecomment-341190909  

Verified.
