# #873 - More explanation of basic_parser move constructor [Closed]

> Username: vinniefalco  
> Created at: 2017-11-06 22:55:17 UTC  
> Updated at: 2017-11-09 09:11:01 UTC  
> Closed at: 2017-11-09 09:11:01 UTC  
> Url: https://github.com/boostorg/beast/issues/873  

The exposition for this function:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/basic_parser/overload3.html  
  
Should have all of the information from this documentation page:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/more_examples/change_body_type.html  
  
and additional exposition with an example for using the constructor to change the commitment of the body type.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-09 04:12:17 UTC  
> Url: https://github.com/boostorg/beast/issues/873#issuecomment-343042373  

Actually, this is misleading. Most special members of `basic_parser` should be protected. It is up to the derived class to determine the meaning and behavior of those members. In fact, `parser` **already** documents the body conversion constructor:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/parser.hpp#L125  
  
So I will mark those members protected instead.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-11-09 09:11:01 UTC  
> Url: https://github.com/boostorg/beast/issues/873#issuecomment-343092307  

Addressed in **version 140**
