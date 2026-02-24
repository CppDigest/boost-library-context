# #147 - Review copied_strings logic [Closed]

> Username: alandefreitas  
> Created at: 2022-03-21 18:22:51 UTC  
> Updated at: 2022-09-02 17:23:49 UTC  
> Closed at: 2022-09-02 17:23:49 UTC  
> Url: https://github.com/boostorg/url/issues/147  

Look into pushing the copied strings logic deeper into the implementation.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-03-21 18:35:50 UTC  
> Url: https://github.com/boostorg/url/issues/147#issuecomment-1074272879  

@vinniefalco Any suggestions on this?   
  
Should we   
  
1) try to use copied_strings at more places? or  
2) try to find patterns to avoid false positives with copied_strings? or  
3) try to expand the logic of copied_strings to range algorithms (`assign`, `emplace_at`, `replace`, etc...)? or  
4) attempt to completely replace copied_strings? or  
5) all of the above?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-03-21 21:35:00 UTC  
> Url: https://github.com/boostorg/url/issues/147#issuecomment-1074443903  

Go through cases where the input string_view might be invalidated.  
  
- We only need the copy when we reallocate, which might only happen in `resize_impl`.  
- `resize_impl` in theory could shift characters left for "Remove "./""  
- Depending on where string view comes from, the copied string might require some form of memmove.  
  
About "remove "./":  
  
- If the URL has no scheme and starts with "./" then it removes the "./". That is, if the URL is "./x" and you set the scheme to "http" you get "http:x" instead of "http:/x".  
- If the URL has a scheme, we might need to include "./". That is, if the URL is "http:y:" and you remove the scheme, you get "./y:" instead of "y:", which would represent another scheme "y".  
  
If all URL modification went through one `modify_impl( string_view s, ... )` function, then it would be trivial to push the copied strings logic down into that function so that `set_<component>_impl` could call `modify_impl( string_view s, ... )` instead of `resize_impl`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-23 22:25:29 UTC  
> Url: https://github.com/boostorg/url/issues/147#issuecomment-1224951735  

The way to do this is to make a custom `memmove` function that also shuffles the `string_view` if needed. The obstacle will be breaking through the abstraction of `any_path` and `any_query`. I did this in http-proto:   
  
https://github.com/CPPAlliance/http_proto/blob/f2382d8eab8be2e9d6e6e14c5502d90ccf55e95f/include/boost/http_proto/detail/move_chars.hpp#L70  
  
Classes derived from `any_path` and `any_query` need to expose their string_view somehow, perhaps making it a member of the abstract base. Then we can pass that view down into the implementation and on to `move_chars`

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-02 17:23:49 UTC  
> Url: https://github.com/boostorg/url/issues/147#issuecomment-1235744076  

This is done, and we just have to apply it to segments (other issue is open for that).
