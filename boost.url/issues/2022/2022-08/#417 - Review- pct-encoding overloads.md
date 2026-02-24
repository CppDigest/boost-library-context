# #417 - Review: pct-encoding overloads [Closed]

> Username: alandefreitas  
> Created at: 2022-08-16 23:57:41 UTC  
> Updated at: 2022-10-10 23:23:18 UTC  
> Closed at: 2022-10-10 23:23:18 UTC  
> Url: https://github.com/boostorg/url/issues/417  

From the review  
  
> I'm happy to see percent-encoding standalone functions being exposed,  
as this is something present in almost all other languages. I'd consider  
exposing higher-level functions here, maybe taking a lvalue reference  
to a std::string, allowing the user to percent-encode strings easier  
(so they resemble JavaScript encodeURIComponent).  
  
Comments:  
  
> Yes, I am not joyous about our current percent-encoding APIs. There's  
a lot of them, with small variations, and some simple operations are  
missing (appending or assigning to a MutableString out-param as you  
pointed out). They are still great but I think they could be greater.  
We will work on them some more, and if you or anyone else would like  
to provide input into that process, it is welcomed.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-22 20:55:30 UTC  
> Url: https://github.com/boostorg/url/issues/417#issuecomment-1223018419  

> Its nice that there are functions to manually do percentage encoding,  
> but I would prefer (additional) simpler functions which just return a  
> std::string.  
>

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-11 23:48:03 UTC  
> Url: https://github.com/boostorg/url/issues/417#issuecomment-1243070855  

Well we need the StringToken in the percent-encoding algorithms

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-11 23:50:57 UTC  
> Updated at: 2022-09-11 23:51:24 UTC  
> Url: https://github.com/boostorg/url/issues/417#issuecomment-1243071429  

the `decode` function should return `result<pct_string_view>` and not `result<decode_view>`  
  
Actually we need to think about that.. lets talk

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-09-12 23:55:14 UTC  
> Url: https://github.com/boostorg/url/issues/417#issuecomment-1244723011  

Yes. If the reviewer wants something like  
  
```javascript  
encodeURIComponent(uriComponent)  
```  
  
the closest thing we can achieve would be renaming `encode_to_string` to `encode` and/or something like:  
  
```cpp  
template<  
    BOOST_URL_STRTOK_TPARAM>  
BOOST_URL_STRTOK_RETURN  
encode(  
    BOOST_URL_STRTOK_ARG(token),  
    encode_opts const& opt = {},  
    CharSet const& allowed = {});  
```  
  
I imagine the decoding algorithms could have an analogous overload, with the main difference being that these could fail and would need to throw if we want to really make them behave like   
  
```javascript  
decodeURIComponent(encodedURI)  
```
