# #70 - Link to wrong enum page [Closed]

> Username: evanlenz  
> Created at: 2021-05-21 06:33:07 UTC  
> Updated at: 2021-09-06 14:56:20 UTC  
> Closed at: 2021-09-06 14:56:20 UTC  
> Url: https://github.com/boostorg/docca/issues/70  

Fix is in progress but outcome is uncertain. See https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/result/overload1.html  
  
`static::unknown` erroneously links to `http::field` (which has `unknown` as one of its values).  
  
This is due to a bad link being output by Doxygen. However, we may be able to use an appropriate heuristic. See the **d:get-target-element()** function in base-extract-xml-pages.xsl

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-07-09 06:12:43 UTC  
> Url: https://github.com/boostorg/docca/issues/70#issuecomment-876941907  

Good news on this one. I was able to add a heuristic to address the issue and fix up Doxygen's bad refid assignment, which fixed a bad link in each of the following pages:  
  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/on_request_impl.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__header/method/overload1.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__header/method/overload2.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__header/result/overload1.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__header/result/overload3.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__int_to_status.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/method/overload1.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/method/overload2.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/result/overload1.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/result/overload3.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__string_to_verb.html  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__to_status_class/overload1.html
