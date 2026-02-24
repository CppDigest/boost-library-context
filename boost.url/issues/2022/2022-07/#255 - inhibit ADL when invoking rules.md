# #255 - inhibit ADL when invoking rules [Open]

> Username: vinniefalco  
> Created at: 2022-07-27 15:59:22 UTC  
> Updated at: 2022-10-19 23:38:18 UTC  
> Url: https://github.com/boostorg/url/issues/255  

algorithms which pass rules must inhibit ADL in the function call. grammar and rfc need review.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-10-19 21:24:07 UTC  
> Url: https://github.com/boostorg/url/issues/255#issuecomment-1284589749  

How do we do that? `std::enable_if_t<is_rule<Rule>::value>`?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-10-19 23:24:51 UTC  
> Url: https://github.com/boostorg/url/issues/255#issuecomment-1284696087  

No you just put the function call in parenthesis, e.g. `(parse)(it, end, r)`. This prevents the compiler from applying argument-dependent lookup.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-10-19 23:38:18 UTC  
> Url: https://github.com/boostorg/url/issues/255#issuecomment-1284704775  

When we call a library function template with types that come from the user, we must ensure that the call inhibits ADL.  
  
Otherwise, the sneaky little bastard users can make our algorithm do something it is not designed to do, which is call an entirely different function under control of the user.  
  
Two easy ways to inhibit ADL:  
  
- use a namespace qualifier on the function, or  
- put the function name in parenthesis (easier)  
  
Here, ADL can't hurt us because we aren't calling a free function. There is no argument-dependent lookup:  
https://github.com/boostorg/url/blob/develop/include/boost/url/grammar/impl/parse.hpp#L36  
  
```cpp  
    return r.parse(it, end);  
```  
  
The old `tag_invoke` system was vulnerable to ADL hijacking  
  
For instance, the user can hijack our encoding functions and possibly our comparison operators. We need to find places in our code where we call our own function templates with user-defined objects.  
  
Another one:  
  
https://github.com/boostorg/url/blob/develop/include/boost/url/detail/encode.hpp#L114  
  
```cpp  
    return encode_unsafe(  
```  
  
The call to `encode_unsafe` needs to inhibit ADL, because unreserved is a user-defined type. It would be nice if the compiler could help us here.   
  
Now if we are calling into detail:: and it has a namespace qualifier, ADL is already inhibited. e.g. if the above read `detail::encode_unsafe`, then no action is needed.  
  
In practice, users don't try to hijack our functions (although this has been known to happen sometimes when a company needs to work around a bug in a library whose source they cannot modify). it can happen when there is very complicated code, people aren't paying attention, and a series of bad luck bad decisions cause the wrong function to be called from our library because of a user overload (edited)
