# #448 - Review: decode functions [Closed]

> Username: alandefreitas  
> Created at: 2022-08-23 20:42:36 UTC  
> Updated at: 2022-08-25 20:40:33 UTC  
> Closed at: 2022-08-25 20:40:33 UTC  
> Url: https://github.com/boostorg/url/issues/448  

Review `pct_decode`/`pct_encode` functions.  
  
Discuss requirements in the documentation.  
  
- Validate characters  
- Report errors when invalid  
- Lazy return type  
- Avoid duplicate throwing and error_code overloads with `result<>`  
- Avoid having extra overloads  
  
Alternatives:  
  
```cpp  
template< class CharSet = grammar::all_chars_t >  
auto  
decode(  
    string_view s,  
    CharSet const& allowed = {},  
    decode_opts const& opt = {}) ->  
        result< decode_view >  
```  
  
```cpp  
result<decode_view> r = decode(...).value();  
decode_view v = decode(...).value();  
```  
  
```cpp  
decode_view s0 = u.host();  
string_view s1 = u.encoded_host();  
```
