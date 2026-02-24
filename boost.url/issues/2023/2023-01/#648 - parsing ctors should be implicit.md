# #648 - parsing ctors should be implicit [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-01-03 05:26:34 UTC  
> Updated at: 2023-01-19 01:23:32 UTC  
> Closed at: 2023-01-19 01:23:32 UTC  
> Url: https://github.com/boostorg/url/issues/648  

Some parsing ctors, e.g. of `url_view` are `explicit` which is really annoying for API usage. E.g.:  
  
```cpp  
auto get(urls::url_view);  
```  
  
should allow me to write `get("https://boost.org")`, but atm I have to write `get(urls::url_view{"https://boost.org"})`.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-01-03 13:48:00 UTC  
> Url: https://github.com/boostorg/url/issues/648#issuecomment-1369789674  

I'm not sure about that. I've always assumed implicit conversions should be used when  
  
- there's a 1-to-1 relationship between the classes, (For instance, an implicit conversion here would allow relative references. Should they be allowed in these requests?)  
- they are very cheap, (There's also the case where the user already has a url and the API would for them to reparse the url with `get(u.buffer())`.) and   
- wouldn't usually throw. (How does `get` report errors? Aren't we losing the ability to report them without exceptions by doing that? )  
  
What about  
  
 ```cpp  
# get.hpp  
auto get(string_view);  
```  
  
 ```cpp  
# get.ipp  
auto get(string_view sv) {  
    auto r = urls::parse_absolute_uri(sv);  
    if (!r)  
        return r.error(); // not sure what auto is  
    // handle *r  
}  
```

---

## Comment 2

> Username: alandefreitas  
> Created at: 2023-01-03 19:51:47 UTC  
> Url: https://github.com/boostorg/url/issues/648#issuecomment-1370164881  

I guess we need implicit constructors and conversions then :thinking:
