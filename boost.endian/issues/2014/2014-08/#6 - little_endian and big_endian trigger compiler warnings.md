# #6 - little_endian and big_endian trigger compiler warnings [Closed]

> Username: ghost  
> Created at: 2014-08-28 15:40:38 UTC  
> Updated at: 2014-08-29 12:13:07 UTC  
> Closed at: 2014-08-29 12:13:07 UTC  
> Url: https://github.com/boostorg/endian/issues/6  

```  
inline void big_endian(Reversible& x) BOOST_NOEXCEPT  
inline void little_endian(Reversible& x) BOOST_NOEXCEPT      
```  
  
These trigger unused variable compiler warnings when used on the native architecture i.e. little_endian on a little endian system:  
  
```  
/path/to/endian/conversion.hpp:398:41: warning: unused parameter 'x' [-Wunused-parameter]  
```  
  
Could the argument name be `#ifdef`ed out?  Or a token operation like `(x)` be done to prevent the warning?

---

## Comment 1

> Username: Beman  
> Created at: 2014-08-29 12:13:07 UTC  
> Url: https://github.com/boostorg/endian/issues/6#issuecomment-53867729  

Fixed. Thanks!  
  
--Beman
