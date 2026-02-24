# #534 - decode_view from pct_string_view [Closed]

> Username: vinniefalco  
> Created at: 2022-09-11 16:16:50 UTC  
> Updated at: 2022-09-14 17:31:24 UTC  
> Closed at: 2022-09-14 17:31:24 UTC  
> Url: https://github.com/boostorg/url/issues/534  

`decode_view` should only be constructible from `pct_string_view` and the Exception Safety and @throw clauses should match.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 16:20:32 UTC  
> Url: https://github.com/boostorg/url/issues/534#issuecomment-1242997378  

Should probably store `pct_string_view` as a data member too, since it already knows the decoded size.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-09-12 18:55:18 UTC  
> Updated at: 2022-09-12 18:56:29 UTC  
> Url: https://github.com/boostorg/url/issues/534#issuecomment-1244160013  

The implicit conversions are making this very difficult to achieve.  
  
- `decode_view pct_string_view::decoded()` and `decode_view pct_string_view::operator*()` (which require including `decode_view` before `pct_string_view`) make a new `decoded_view::decoded_view(pct_string_view)` constructor a circular dependency (which requires including `pct_string_view` before `decode_view`).  
- A new explicit `pct_string_view::operator decode_view() const noexcept` conversion doesn't work because it's ambiguous with the other `decode_view` constructors when we consider the implicit conversions.  
  
That's the output in the first case:  
  
```  
/boost/libs/url/test/unit/decode.cpp:12:  
/boost/libs/url/include/boost/url/decode.hpp:16:  
/boost/libs/url/include/boost/url/decode_view.hpp:16:  
/boost/libs/url/include/boost/url/pct_string_view.hpp:343:5: error: unknown type name 'decode_view'  
    decode_view  
    ^  
/boost/libs/url/include/boost/url/pct_string_view.hpp:362:5: error: unknown type name 'decode_view'  
    decode_view  
    ^  
/boost/libs/url/include/boost/url/pct_string_view.hpp:347:24: error: no member named 'make_decode_view' in namespace 'boost::urls::detail'  
        return detail::make_decode_view(  
               ~~~~~~~~^  
```  
  
That's the output in the second case:  
  
```  
/boost/libs/url/test/unit/pct_string_view.cpp:273:21: error: call to constructor of 'boost::urls::decode_view' is ambiguous  
        decode_view d( str );  
                    ^  ~~~  
/boost/libs/url/include/boost/url/decode_view.hpp:71:7: note: candidate constructor (the implicit move constructor)  
class decode_view  
      ^  
/boost/libs/url/include/boost/url/decode_view.hpp:71:7: note: candidate constructor (the implicit copy constructor)  
/boost/libs/url/include/boost/url/decode_view.hpp:194:5: note: candidate constructor  
    decode_view(  
    ^  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-12 19:23:20 UTC  
> Url: https://github.com/boostorg/url/issues/534#issuecomment-1244220908  

wut
