# #592 - Unify encoding options [Closed]

> Username: alandefreitas  
> Created at: 2022-10-11 16:42:55 UTC  
> Updated at: 2022-10-24 19:43:38 UTC  
> Closed at: 2022-10-24 19:43:38 UTC  
> Url: https://github.com/boostorg/url/issues/592  

Combine `encode_opts` and `decode_opts` into one struct  
  
```cpp  
struct encoding_opts  
{  
    bool space_is_plus = false;  
    bool plus_is_reserved = false;  
    bool lower_case = false;  
    bool disallow_null = false;  
    bool disallow_non_normal = false;  
};  
```  
  
Otherwise, `params_ref` and `params_view` will have 2 options structs that could need setting.  
  
This way there is only one struct, and it applies to both encoding and decoding.  
  
The user could create and store a single option and have round trips.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-10-11 23:19:10 UTC  
> Updated at: 2022-10-11 23:21:49 UTC  
> Url: https://github.com/boostorg/url/issues/592#issuecomment-1275393339  

We can strike two of them and leave  
  
```cpp  
struct encoding_opts  
{  
    bool space_is_plus = false;  
    bool lower_case = false;  
    bool disallow_null = false;  
};  
```
