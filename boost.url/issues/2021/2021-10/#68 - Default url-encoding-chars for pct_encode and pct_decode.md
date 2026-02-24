# #68 - Default url-encoding-chars for pct_encode and pct_decode [Closed]

> Username: vinniefalco  
> Created at: 2021-10-14 17:19:45 UTC  
> Updated at: 2021-12-27 21:27:03 UTC  
> Closed at: 2021-12-27 21:27:03 UTC  
> Url: https://github.com/boostorg/url/issues/68  

e.g.  
```  
template<  
    class CharSet =  
        url_encoding_chars,  
    class Allocator =  
        std::allocator<char> >  
std::basic_string<char,  
    std::char_traits<char>,  
        Allocator>  
pct_encode(  
    string_view s,  
    CharSet const& cs = {},  
    pct_encode_opts const& opt = {},  
    Allocator const& a = {});  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-12-27 18:43:42 UTC  
> Url: https://github.com/boostorg/url/issues/68#issuecomment-1001696435  

The character set is called `reserved_chars_t`
