# #121 - Wrong pointer value in from_chars long-double fallback [Closed]

> Username: Flamefire  
> Created at: 2024-01-17 11:26:32 UTC  
> Updated at: 2024-01-22 08:23:24 UTC  
> Closed at: 2024-01-22 08:23:24 UTC  
> Url: https://github.com/boostorg/charconv/issues/121  

There is a bug at https://github.com/cppalliance/charconv/blob/06d834728ae8970473f6ef18c079f0e7fae7b071/src/from_chars.cpp#L317  
  
`std::strtold` is called with a pointer to a temporary std::string and a pointer relative to that is then returned by the function which after the function is invalid/dangling.  
  
It also looks like this is duplicating an existing function that doesn't has this issue and is even better by using a local buffer before falling back to allocating: https://github.com/cppalliance/charconv/blob/06d834728ae8970473f6ef18c079f0e7fae7b071/include/boost/charconv/detail/from_chars_float_impl.hpp#L38 &  
https://github.com/cppalliance/charconv/blob/06d834728ae8970473f6ef18c079f0e7fae7b071/include/boost/charconv/detail/from_chars_float_impl.hpp#L101  
  
This should probably be tested too.

---

## Comment 1

> Username: mborland  
> Created at: 2024-01-18 10:18:08 UTC  
> Url: https://github.com/boostorg/charconv/issues/121#issuecomment-1898191163  

Testing is added here along with locale support: https://github.com/cppalliance/charconv/pull/123. Once it's merged I'll make the change. I am not sure when I replaced `from_chars_strtod` in `from_chars` but it was mistake to do so.

---

## Comment 2

> Username: Flamefire  
> Created at: 2024-01-18 11:58:31 UTC  
> Url: https://github.com/boostorg/charconv/issues/121#issuecomment-1898343799  

I don't see a test added for the issue reported here. I expected something like the lines of:  
```  
const char buffer[] = "1.1897e+2";  
T v = 0;  
auto r = boost::charconv::detail::from_chars_strtod(buffer, buffer + sizeof(buffer), v);  
BOOST_TEST(r);  
BOOST_TEST(v == 1.1897e+2);  
BOOST_TEST(r.ptr == buffer + sizeof(buffer));  
```  
  
But given that this only checks this single case and the issue is triggered by the "roundtrip" and "limits" test I'd rather suggest to add checking the value of `r.ptr` throughout your test suite. I.e. for every `BOOST_TEST(r)` there should likely be a `BOOST_TEST(r.ptr == buffer_end)` unless it is expected that the buffer is only partially parsed which of course also needs to be tested. I.e.:  
```  
const char buffer[] = "42abcd";  
T v = 0;  
auto r = boost::charconv::detail::from_chars_strtod(buffer, buffer + sizeof(buffer), v);  
BOOST_TEST(r);  
BOOST_TEST(v == 42);  
BOOST_TEST(r.ptr == buffer + 2);  
```
