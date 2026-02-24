# #882 - parse_unicode_url function [Open]

> Username: alandefreitas  
> Created at: 2024-11-11 18:58:35 UTC  
> Updated at: 2026-01-29 18:16:24 UTC  
> Url: https://github.com/boostorg/url/issues/882  

We should have a function to handle unicode urls:  
  
```cpp  
result<url> parse_unicode_url(...)  
```  
  
To convert to a valid URL, the host would be converted with punycode, and other components would percent-escape when possible. Errors are still possible.   
  
We can identify components by identifying possible delimiters (like `urls::format` does) or we can provide functions that create a URL from its components:  
  
```cpp  
result<url>  
make_url(  
  string_view scheme,  
  utf8_string_view authority,  
  utf8_string_view path,  
  utf8_string_view query,  
  utf8_string_view fragment)  
{  
    url u;  
    u.reserve(...);  
    u.set_scheme(scheme);  
    u.set_authority(authority);  
    u.set_path(path);  
    // ...  
}  
  
result<url>  
make_iri(  
  string_view scheme,  
  utf8_string_view authority,  
  utf8_string_view path,  
  utf8_string_view query,  
  utf8_string_view fragment)  
{  
    url u;  
    u.reserve(...);  
    u.set_scheme(scheme);  
    u.set_authority(detail::parse_punycode(authority));  
    u.set_path(detail::pct_encode(path));  
    // ...  
}  
```

---

## Comment 1

> Username: luz-arreola  
> Created at: 2024-11-18 18:46:17 UTC  
> Url: https://github.com/boostorg/url/issues/882#issuecomment-2483845663  

It is probably a very good idea to use ICU for this.  I currently use ICU to create and parse unicode URLs.  It works perfectly with all types of Unicode characters.  Adding ICU support for Boost.URL would be excellent!  Alternatively, there are some very lightweight unicode libraries that I think would work for this, but ICU is the only fully featured unicode library.  People who work with unicode very likely already use many of the features only provided by ICU I believe.
