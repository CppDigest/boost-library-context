# #54 - source_location in error codes [Closed]

> Username: vinniefalco  
> Created at: 2021-10-03 17:58:33 UTC  
> Updated at: 2021-10-03 19:16:00 UTC  
> Closed at: 2021-10-03 19:16:00 UTC  
> Url: https://github.com/boostorg/url/issues/54  

Error assignments should use a macro that also sets the `source_location`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-03 18:09:22 UTC  
> Url: https://github.com/boostorg/url/issues/54#issuecomment-932999187  

We have this now  
```  
#define BOOST_URL_ERR(ev) (::boost::system::error_code( (ev), [] { \  
        static constexpr auto loc(BOOST_CURRENT_LOCATION); \  
        return &loc; }()))  
```
