# #55 - result's ptr is not correct for some numbers [Closed]

> Username: grisumbras  
> Created at: 2023-05-30 11:06:19 UTC  
> Updated at: 2023-06-01 08:39:28 UTC  
> Closed at: 2023-06-01 08:39:28 UTC  
> Url: https://github.com/boostorg/charconv/issues/55  

This fails:  
```c++  
string_view s = "0e0";  
auto err = detail::charconv::from_chars( s.begin(), s.end(), d );  
BOOST_ASSERT( err.ptr == s.end() );  
```  
  
Same for e.g. `0e00000000000` and `0e1`.

---

## Comment 1

> Username: mborland  
> Created at: 2023-05-30 14:25:43 UTC  
> Url: https://github.com/boostorg/charconv/issues/55#issuecomment-1568531840  

This looks to be fixed by the change to fast_float.
