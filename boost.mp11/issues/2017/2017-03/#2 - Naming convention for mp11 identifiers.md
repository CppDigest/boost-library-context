# #2 - Naming convention for mp11 identifiers [Closed]

> Username: glenfe  
> Created at: 2017-03-17 16:45:08 UTC  
> Updated at: 2017-07-31 19:44:25 UTC  
> Closed at: 2017-07-31 19:44:25 UTC  
> Url: https://github.com/boostorg/mp11/issues/2  

Suggestion: Instead of both the `mp11` namespace and the `mp_` prefix, is the following naming scheme any better? `mp11::identifier_`   
  
i.e. Keep the `mp11` namespace, but just use an underscore suffix instead of the current prefix. It still allows you to use keywords (`mp11::if_`) while making the resulting code shorter to read (and type).  
  
(Granted a trailing underscore is not orthodox in most cases, but maybe for a MP library's primitives it may appear sane).

---

## Comment 1

> Username: odinthenerd  
> Created at: 2017-03-17 16:53:30 UTC  
> Url: https://github.com/boostorg/mp11/issues/2#issuecomment-287410128  

Trailing underscore suffix is a somewhat common pattern in TMP in my opinion. Think of int_ bool_ etc. I support the change.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-03-17 16:54:56 UTC  
> Url: https://github.com/boostorg/mp11/issues/2#issuecomment-287410536  

I originally had everything in namespace `boost`, but moved things into `mp11` to allow  
  
```  
using namespace boost::mp11;  
```  
  
because individual using declarations proved a bit tiresome.  
  
So in the typical case, one sees `mp_unique<mp_transform<...>>`. `mp11::unique_<mp11::transform_<...>>` is not much worse, but it's not better either.

---

## Comment 3

> Username: glenfe  
> Created at: 2017-03-21 11:40:51 UTC  
> Url: https://github.com/boostorg/mp11/issues/2#issuecomment-288053248  

Granted, the using namespace declaration helps. I was thinking that in header-only libraries, where MP11 will no doubt be used most, this may not be desirable to some library developers:  
  
```cpp  
namespace library {  
  
using namespace boost::mp11;  
// use mp_identifier  
  
} /* library */  
```  
e.g. They may not want their users to have access to mp11 identifiers within their library namespace. In that case, such hypothetical library developers could do:  
* `namespace mp = boost::mp11;` and using `mp::mp_identifier`  
* Just using the full`boost::mp11::mp_identifier` everywhere  
  
I guess the former works, and is as tidy as `mp11::identifier_`.
