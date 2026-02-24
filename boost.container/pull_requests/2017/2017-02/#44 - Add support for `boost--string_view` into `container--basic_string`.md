# #44 Add support for `boost::string_view` into `container::basic_string` [Merged]

> Username: mclow  
> Created at: 2017-02-13 19:35:48 UTC  
> Updated at: 2017-02-21 13:27:38 UTC  
> Merged at: 2017-02-21 13:27:38 UTC  
> Closed at: 2017-02-21 13:27:38 UTC  
> Url: https://github.com/boostorg/container/pull/44  

Conversion from a `basic_string` to a `string_view`, and make all the comparisons work.  
  
Ion - if you like this, I'll work up some tests, too.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-02-14 16:35:41 UTC  
> Updated_at: 2017-02-14 20:28:48 UTC  
> Url: https://github.com/boostorg/container/pull/44#issuecomment-279760348  

Thanks Marshall. I need to think about it, maybe a templated solution might be a better approach (interoperability with any "view") to avoid injecting boost::string_view dependencies and to support  std::string_view and other user-defined views. In any case, your patch is a good starting point.

---
