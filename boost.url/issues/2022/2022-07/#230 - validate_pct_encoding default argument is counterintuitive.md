# #230 - validate_pct_encoding default argument is counterintuitive [Closed]

> Username: alandefreitas  
> Created at: 2022-07-13 20:57:07 UTC  
> Updated at: 2022-07-19 20:40:08 UTC  
> Closed at: 2022-07-19 20:40:08 UTC  
> Url: https://github.com/boostorg/url/issues/230  

## TL;DR  
  
The default `CharSet const& cs` in `validate_pct_encoding` is counterintuitive because `reserved_chars_t{}` would accept everything that should be rejected and reject everything that should be accepted.  
  
## Context  
  
```cpp  
template<  
    class CharSet = reserved_chars_t>  
std::size_t  
validate_pct_encoding(  
    string_view s,  
    error_code& ec,  
    pct_decode_opts const& opt = {},  
    CharSet const& cs = {}) noexcept;  
```  
  
The name `cs` is not clear about whether these are the chars that will be accepted or rejected. I've always assumed it was the rejected chars, because of the default parameter `reserved_chars_t{}`.   
  
This assumption was wrong but never caused any problems because `validate_pct_encoding` is never called without a explicit `Charset` in Boost.URL.  
  
## Proposed solution  
  
The default charset would be:  
  
```cpp  
[](unsigned char ch) { return ch != '%'; };  
```  
  
or, if the function already handles `c == %`, then simply  
  
```cpp  
[](unsigned char ch) { return true; };  
```  
  
## Rationale  
  
The parameter `cs` exists to handle the special cases of URI components that have requirements _besides_ percent encoding. That is, it defines the characters that should be rejected _besides_ `%` in a string because of the component grammar and _despite_ percent-encoding. These are usually the chars that would be ambiguous as boundaries with other components.  
  
In the general case, when decoding an arbitrary percent-encoded string, these extra URI requirements don't apply. The user should be able to decode anything without any arbitrary requirements of a specific URI component. In other words, anything but `%` should be accepted as non-encoded chars.   
  
This would make `validate_pct_encoding` coherent with `pct_decode_unchecked`, which doesn't need a charset, since everything that's not `%` should be considered valid at this point. This also helps us note that simply negating `reserved_chars_t` would also include arbitrary constraints.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-13 22:46:33 UTC  
> Url: https://github.com/boostorg/url/issues/230#issuecomment-1183755836  

Yes this looks like we have a few problems in percent-encoding algorithms

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-07-14 01:19:32 UTC  
> Url: https://github.com/boostorg/url/issues/230#issuecomment-1183843864  

1. rename `cs` to `allowed`  
  
2. separate overloads  
  
```cpp  
template<class CharSet>  
std::size_t  
validate_pct_encoding(  
    string_view s,  
    error_code& ec,  
    CharSet const& allowed,  
    pct_decode_opts const& opt = {}) noexcept;  
  
BOOST_URL_DECL  
std::size_t  
validate_pct_encoding(  
    string_view s,  
    error_code& ec,  
    pct_decode_opts const& opt = {}) noexcept;  
```  
  
3. maybe lift `opt.plus_to_space` into two loops in `validate_pct_encoding` since it can't change during the loop  
  
4. add to `validate_pct_encoding`  
  
```cpp  
    BOOST_ASSERT(! opt.plus_to_space || allowed('+'));  
```  
  
5. if `plus_to_space == true` should we require the allowed `CharSet` include `+`?  
  
6. change `cs` to `allowed` in url.ipp  
  
7. break `pct_decode` overload with `CharSet` into 2 functions (one with `CharSet` one without)  
  
8. remove `pct_decode` overload that returns `basic_string`  
  
9. remove `pct_decode_to_value`  
  
10. remove `pct_decode_unchecked` returning `const_string`  
  
11. remove default value for `CharSet` in `pct_encode_bytes`  
  
12. remove `CharSet` default value from `pct_encode` overload  
  
13. rename `pct_encode` which returns `basic_string`, to `pct_encode_to_string`  
  
14. add `pct_encode_append_to` and `pct_encode_assign_to`  
  
15. remove `pct_encode_to_value` that returns `const_string`
