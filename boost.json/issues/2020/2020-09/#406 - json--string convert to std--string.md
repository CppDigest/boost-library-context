# #406 - json::string convert to std::string [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 18:18:25 UTC  
> Updated at: 2024-04-09 19:24:58 UTC  
> Closed at: 2024-04-09 19:24:58 UTC  
> Url: https://github.com/boostorg/json/issues/406  

Might be nice to have `string::to_std_string` although this means including `<string>`.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-05-23 18:52:50 UTC  
> Url: https://github.com/boostorg/json/issues/406#issuecomment-1135026323  

1. `core/detail/string_view.hpp` includes `<string>` so, not including it in JSON is moot.  
2. `std::string s = json::string_view(json::string());` works. And with C++17 `auto s = std::string(json::string());` also works. Is this good enough?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-24 00:49:37 UTC  
> Url: https://github.com/boostorg/json/issues/406#issuecomment-1135280280  

Given  
```  
json::string js;  
```  
  
My intuition tells me that users want the expression `js.to_string()` (alternatively: `js.to_std_string()`) to return a `std::string`. This clearly expresses intent and can be searched. But I could be convinced otherwise. What is typical practice here?

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-05-24 07:30:36 UTC  
> Url: https://github.com/boostorg/json/issues/406#issuecomment-1135512511  

QString has toStdString, but I don't think Qt is the best example of model C++ API. I'm personally perfectly Ok with a conversion (even though it's a bit obscure in C++11 mode). So, let's keep the issue for now and see if users actually ask for a dedicated conversion function.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-05-24 13:26:52 UTC  
> Url: https://github.com/boostorg/json/issues/406#issuecomment-1135923340  

I would prefer if users simply worked with the native `json::string` since that is the most efficient. Making it easy to convert to `std::string` only encourages inefficiency.

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-04-08 14:29:22 UTC  
> Url: https://github.com/boostorg/json/issues/406#issuecomment-2042906450  

Still no issues about this. I'm inclined to close this.
