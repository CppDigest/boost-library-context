# #845 - Leaner .text and .data segments for embedded [Closed]

> Username: vinniefalco  
> Created at: 2017-10-28 16:34:12 UTC  
> Updated at: 2022-06-16 16:35:10 UTC  
> Closed at: 2022-06-16 16:35:10 UTC  
> Url: https://github.com/boostorg/beast/issues/845  

For embedded devices with resource constraints, it would be nice to allow customization and provide a preprocessor directive to reduce the size of the .text and .data segments.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-28 16:39:58 UTC  
> Url: https://github.com/boostorg/beast/issues/845#issuecomment-340203782  

There's a big table of field names here:  
https://github.com/boostorg/beast/blob/caf144388778ecb59ff011d10c2afc38b62e5b1a/include/boost/beast/http/impl/field.ipp#L88  
  
`basic_parser` calls `string_to_field` which forces the instantiation of the table. To relieve this, `basic_parser` can call a member function of the derived class instead. Callers can provide their own derived class which performs the lookup using a simplified algorithm.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-28 16:50:22 UTC  
> Url: https://github.com/boostorg/beast/issues/845#issuecomment-340204408  

`BOOST_BEAST_NO_PARSER_TABLES` can control whether the parser uses tables for character classes or whether it uses switch and conditional machinery, to reduce the amount of data:  
https://github.com/boostorg/beast/blob/caf144388778ecb59ff011d10c2afc38b62e5b1a/include/boost/beast/http/detail/basic_parser.hpp#L55

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-28 16:54:59 UTC  
> Updated at: 2017-10-28 16:55:06 UTC  
> Url: https://github.com/boostorg/beast/issues/845#issuecomment-340204707  

`string_to_verb` can be a customization point of the `basic_parser` derived class:  
https://github.com/boostorg/beast/blob/caf144388778ecb59ff011d10c2afc38b62e5b1a/include/boost/beast/http/impl/verb.ipp#L79

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-10-28 17:46:10 UTC  
> Url: https://github.com/boostorg/beast/issues/845#issuecomment-340208093  

WebSocket permessage-deflate can be disabled at compile-time: #849

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-06-16 16:35:10 UTC  
> Url: https://github.com/boostorg/beast/issues/845#issuecomment-1157886165  

We did what we could
