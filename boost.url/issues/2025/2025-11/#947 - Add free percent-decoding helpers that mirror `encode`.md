# #947 - Add free percent-decoding helpers that mirror `encode` [Closed]

> Username: alandefreitas  
> Created at: 2025-11-10 17:43:19 UTC  
> Updated at: 2026-01-19 21:00:58 UTC  
> Closed at: 2026-01-19 21:00:58 UTC  
> Url: https://github.com/boostorg/url/issues/947  

Currently, the recommended way to decode a custom string looks like:  
  
```c++  
auto checked = urls::pct_string_view(raw);  
std::string out = checked.decode();  
```  
  
or   
  
```c++  
core::string_view raw = "user%20input";  
auto res = make_pct_string_view(raw);  // system::result<pct_string_view>  
if (res)  
    std::string decoded = res->decode();  
else  
    handle_error(res.error());  
```  
  
That workflow is not ergonomic for users decoding ad-hoc strings (form payloads, config blobs, etc.) because it forces an intermediate view and, unless they discover `make_pct_string_view`, also forces exception-based validation.  
  
Percent-encoding already has public helpers in [`include/boost/url/encode.hpp`](https://github.com/boostorg/url/blob/develop/include/boost/url/encode.hpp#L25-L189) (private functions are in [`include/boost/url/detail/encode.hpp`](https://github.com/boostorg/url/blob/develop/include/boost/url/detail/encode.hpp#L24-L131)). There is no matching free `decode(...)` API: callers must build a [`pct_string_view`](https://github.com/boostorg/url/blob/develop/include/boost/url/pct_string_view.hpp#L257-L359) or [`decode_view`](https://github.com/boostorg/url/blob/develop/include/boost/url/decode_view.hpp#L40-L200) even when they just need something like the `detail::decode_unsafe` free function from [`src/detail/decode.hpp`](https://github.com/boostorg/url/blob/develop/src/detail/decode.hpp#L22-L39).   
  
It would be nicer to mirror the encode API: `decoded_size`, `decode(dest, size, pct_string_view, opts)` (plus `_unsafe`), and a `StringToken` overload that works the same way [`pct_string_view::decode_impl`](https://github.com/boostorg/url/blob/develop/src/pct_string_view.cpp#L21-L32) already does.   
  
**Open questions**  
  
- Decide whether these declarations live beside `encode` or in a new `decode.hpp`.  
- Decide which API for the function:  
    - Replicate the pattern with `pct_string_view` input and `typename StringToken::result_type` (maintains the pattern we use in the rest of the library), or  
    - Offer a `core::string_view` + `result<typename StringToken::result_type>` function (safer for users unaware of how the conversions between string_view and pct_string_view work)
