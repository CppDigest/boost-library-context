# #945 - feat: convenience accessor for query parameters [Closed]

> Username: alandefreitas  
> Created at: 2025-11-08 02:34:15 UTC  
> Updated at: 2026-01-19 18:59:58 UTC  
> Closed at: 2026-01-19 18:59:58 UTC  
> Url: https://github.com/boostorg/url/issues/945  

### Context  
  
Currently, to get a parameter with a fallback, you must write:  
  
```cpp  
auto p = u.params();  
auto it = p.find("id");  
if (it != p.end())  
    use(*it);  
else  
    use("na");  
```  
  
### Proposal  
  
Add a shorthand:  
  
```cpp  
u.params().get_or("id", "na"); // returns "na" if not found  
```  
  
### Internal Discussion  
  
11/07/2025  
  
- Peter Dimov pointed out that `value_or` is a method name reserved for `optional` types (`.value_or(def)`).  
- `get_or` aligns with the naming direction discussed in WG21 proposal [P3091R4: `get` for associative containers](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2025/p3091r4.html) and [§ “Other names for get”](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2025/p3091r4.html#other-names-for-get).  
- Peter noted that in his own code this is typically called `lookup()` returning a pointer, but `get_or` is acceptable since it’s clear and consistent.  
- The committee papers [P3857R0](https://isocpp.org/files/papers/P3857R0.html) and [P3091R4](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2025/p3091r4.html) show that the final naming direction for standard containers is still unsettled but likely to be `get`, so `get_or` matches the expected C++ naming evolution (`get` and `get_or` pairs).  
- Avoids confusion with `optional::value_or`.  
  
#### Next Steps  
   
- Confirm naming (`get_or`)  
- Implement in both `params_encoded_view` and `params_view`.  
  
**References:**   
  
- [`boost::urls::params_encoded_view`](https://www.boost.org/doc/libs/latest/doc/antora/url/reference/boost/urls/params_encoded_view.html)    
- [P3091R4 — get for associative containers](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2025/p3091r4.html)    
- [P3857R0 — get() for associative containers (follow-up)](https://isocpp.org/files/papers/P3857R0.html)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-11-08 02:36:45 UTC  
> Url: https://github.com/boostorg/url/issues/945#issuecomment-3505643764  

`get_or` is good and can we make the alternative a parameter that defaults to empty string?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2025-11-08 02:47:18 UTC  
> Url: https://github.com/boostorg/url/issues/945#issuecomment-3505662271  

And can I also say this is a very nicely written issue

---

## Comment 3

> Username: alandefreitas  
> Created at: 2025-11-08 03:50:33 UTC  
> Url: https://github.com/boostorg/url/issues/945#issuecomment-3505762947  

> we make the alternative a parameter that defaults to empty string?  
  
Got it. So...  
  
```cpp  
pct_string_view  
get_or(  
    pct_string_view key,  
    pct_string_view value = {},  
    ignore_case_param ic = {}) const noexcept;  
```  
  
like https://www.boost.org/doc/libs/latest/doc/antora/url/reference/boost/urls/params_encoded_base/find-0c.html
