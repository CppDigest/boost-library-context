# #540 - re_encoded interface [Closed]

> Username: vinniefalco  
> Created at: 2022-09-13 00:25:26 UTC  
> Updated at: 2022-09-14 00:32:28 UTC  
> Closed at: 2022-09-14 00:32:28 UTC  
> Url: https://github.com/boostorg/url/issues/540  

Right now users cannot implement the same interfaces possible in the library, because they have no re-encoding functions. We might consider adding  
  
```  
template< class CharSet = grammar::all_chars_t >  
std::size_t  
re_encoded_size(  
    pct_string_view const& s,  
    encode_opts const& opt,  
    CharSet const& allowed = {} ) noexcept;  
```  
  
and  
  
```  
template<  
  class CharSet = grammar::all_chars_t,  
  class StringToken = string_token::return_string >  
typename StringToken::result_type  
re_encode(  
    pct_string_view const& s,  
    encode_opts const& opt,  
    CharSet const& allowed = {},  
    StringToken&& token = {} ) noexcept;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-13 04:11:45 UTC  
> Url: https://github.com/boostorg/url/issues/540#issuecomment-1244873369  

Unfortunately this API doesn't work because StringToken can't capture the needs of writing to a char buffer

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-14 00:32:28 UTC  
> Url: https://github.com/boostorg/url/issues/540#issuecomment-1246087495  

Closing this for now until there is user demand.
