# #375 - authority_view::op== [Closed]

> Username: alandefreitas  
> Created at: 2022-08-09 02:15:03 UTC  
> Updated at: 2022-08-09 15:16:53 UTC  
> Closed at: 2022-08-09 15:09:23 UTC  
> Url: https://github.com/boostorg/url/issues/375  

Expressions such as  
  
```cpp  
assert(u.authority() == "user:pass@example.com:443");  
```  
  
don't work.   
  
All other analogous classes work.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-09 13:03:46 UTC  
> Updated at: 2022-08-09 13:04:02 UTC  
> Url: https://github.com/boostorg/url/issues/375#issuecomment-1209355420  

hmm no I don't agree. this for example doesn't work:  
```  
url_view u( "https://www.example.com/index.htm?text=none#a1" );  
  
assert( u == "https://www.example.com/index.htm?text=none#a1" );  
```  
  
if you want `u.authority()` to "work" then it would have to return `pct_encoded_view`. That could work in theory. But then we need another way to get `authority_view` from `urll_view_base`.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-09 15:09:23 UTC  
> Url: https://github.com/boostorg/url/issues/375#issuecomment-1209509354  

That's true. What confused me is the example of the main components with `assert` didn't work:  
  
```cpp  
         string_view s = "https://user:pass@example.com:443/path/to/my%2dfile.txt?id=42&name=John%20Doe+Jingleheimer%2DSchmidt#page%20anchor";  
        url_view u = parse_uri( s ).value();  
        assert(u.scheme() == "https");  
        assert(u.authority() == "user:pass@example.com:443");  
        assert(u.path() == "/path/to/my-file.txt");  
        assert(u.query() == "id=42&name=John Doe Jingleheimer-Schmidt");  
        assert(u.fragment() == "page anchor");  
```  
  
while   
  
```cpp  
        string_view s = "https://user:pass@example.com:443/path/to/my%2dfile.txt?id=42&name=John%20Doe+Jingleheimer%2DSchmidt#page%20anchor";  
        url_view u = parse_uri( s ).value();  
        std::cout << u.scheme() << "\n";  
        std::cout << u.authority() << "\n";  
        std::cout << u.path() << "\n";  
        std::cout << u.query() << "\n";  
        std::cout << u.fragment() << "\n";  
```  
  
used to work because `op<<` is defined but `op==` is not.  
  
In any case, not returning `authority_view` would be worse than not defining `op==`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-09 15:16:53 UTC  
> Url: https://github.com/boostorg/url/issues/375#issuecomment-1209518693  

`authority` is a compound field. Although, I think they all use the same reserved characters for percent-escapes, so we could in theory offer both `encoded_authority` and `authority` which might be useful. But we have to come up with a new name for the function that returns `authority_view`. It could be `url_view_base::authority_part` or something
