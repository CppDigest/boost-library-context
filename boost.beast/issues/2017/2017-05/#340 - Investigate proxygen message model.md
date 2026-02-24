# #340 - Investigate proxygen message model [Closed]

> Username: vinniefalco  
> Created at: 2017-05-05 03:56:15 UTC  
> Updated at: 2022-06-16 20:48:57 UTC  
> Closed at: 2022-06-16 16:33:07 UTC  
> Url: https://github.com/boostorg/beast/issues/340  

Proxygen has a complex message model tailored for specific use cases:  
https://github.com/facebook/proxygen/blob/master/proxygen/lib/http/HTTPHeaders.h  
  
It should be possible to write a suitable beast Fields wrapper that transports a `proxygen::HTTPMessage` so it can be packaged into a `beast::message` container.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-07 04:56:31 UTC  
> Url: https://github.com/boostorg/beast/issues/340#issuecomment-299682507  

One solution is to use argument dependent lookups as a customization point for serialization, e.g. a call to a non-namespace qualified function, e.g.  
```  
template<class DynamicBuffer, class Fields>  
void  
write_fields(DynamicBuffer& buffer, Fields const& f);  
```  
  
This can be overloaded for user defined fields types. This doesn't address the handling of the start line or the trailers. But perhaps it can be extended. This solves the problem where proxygen fields use the visitor pattern instead of iterators.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-21 02:41:38 UTC  
> Updated at: 2017-06-21 02:41:49 UTC  
> Url: https://github.com/boostorg/beast/issues/340#issuecomment-309946741  

There is progress here with **FieldsReader**  
http://vinniefalco.github.io/beast/beast/concept/FieldsReader.html

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-19 01:47:50 UTC  
> Url: https://github.com/boostorg/beast/issues/340#issuecomment-484742308  

This no longer seems important, but the HTTP Refactor should review Proxygen's features when redesigning the header container.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-16 16:33:07 UTC  
> Url: https://github.com/boostorg/beast/issues/340#issuecomment-1157883479  

proxygen's message model sucks :)

---

## Comment 5

> Username: sehe  
> Created at: 2022-06-16 20:48:57 UTC  
> Url: https://github.com/boostorg/beast/issues/340#issuecomment-1158115822  

> proxygen's message model sucks :)  
  
ROFL progressing insight :)
