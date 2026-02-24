# #883 - Implement beast::istream() [Open]

> Username: djarek  
> Created at: 2017-11-11 01:36:07 UTC  
> Updated at: 2024-06-06 05:34:18 UTC  
> Url: https://github.com/boostorg/beast/issues/883  

This would make the API symmetric in use. E.g.:  
```  
int i, j;  
beast::istream(body) >> i;  
beast::istream(body) >> j;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-11 01:37:19 UTC  
> Url: https://github.com/boostorg/beast/issues/883#issuecomment-343629855  

This should be done by modifying `detail::ostream_buffer` to fulfill the **get** contract of `std::basic_streambuf`.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:44 UTC  
> Url: https://github.com/boostorg/beast/issues/883#issuecomment-384003617  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: ivan-volnov  
> Created at: 2020-12-24 14:11:35 UTC  
> Url: https://github.com/boostorg/beast/issues/883#issuecomment-750893773  

I'm looking forward to have this feature implemented
