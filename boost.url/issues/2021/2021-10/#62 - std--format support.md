# #62 - std::format support [Closed]

> Username: vinniefalco  
> Created at: 2021-10-12 17:35:40 UTC  
> Updated at: 2022-03-12 03:10:26 UTC  
> Closed at: 2022-03-12 03:10:26 UTC  
> Url: https://github.com/boostorg/url/issues/62  

> std::format has its own customization mechanism

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-12 17:46:52 UTC  
> Url: https://github.com/boostorg/url/issues/62#issuecomment-941233395  

> derive the formatter specialization from the one for string_view and inherit parse from there

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-10-12 17:52:30 UTC  
> Url: https://github.com/boostorg/url/issues/62#issuecomment-941237744  

```  
template <> struct std::formatter<muh_type>: std::formatter<std::string_view> {  
  // parse is inherited from formatter<string_view>.  
  template <typename FormatContext>  
  auto format(muh_type const& mt, FormatContext& ctx) {  
    return formatter<string_view>::format(mt.get_string_view(), ctx);  
  }  
};  
```

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-02-15 23:36:24 UTC  
> Url: https://github.com/boostorg/url/issues/62#issuecomment-1040907312  

`fmt` includes a header `fmt/ostream.h` that supports anything `cout` supports.   
The formatter would be nice but it's not essential to make it work with `fmt`.   
  
An `operator string_view` probably works with `fmt` too, but including that in tests would require a dependency outside boost.
