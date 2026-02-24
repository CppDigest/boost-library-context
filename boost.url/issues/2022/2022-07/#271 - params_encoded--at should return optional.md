# #271 - params_encoded::at should return optional [Closed]

> Username: alandefreitas  
> Created at: 2022-07-28 22:05:37 UTC  
> Updated at: 2022-09-03 20:12:11 UTC  
> Closed at: 2022-09-03 20:12:10 UTC  
> Url: https://github.com/boostorg/url/issues/271  

at() is unintuitive because it skips matching keys when has_value==false  
  
optional<string_view> would communicate the difference between an empty value and no value.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-03 20:12:10 UTC  
> Url: https://github.com/boostorg/url/issues/271#issuecomment-1236192203  

I simply removed the at function. We can add it later if users ask.
