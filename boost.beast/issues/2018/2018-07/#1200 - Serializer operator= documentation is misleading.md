# #1200 - Serializer operator= documentation is misleading [Closed]

> Username: djarek  
> Created at: 2018-07-19 22:00:11 UTC  
> Updated at: 2022-06-21 23:23:20 UTC  
> Closed at: 2022-06-21 23:23:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1200  

This suggests that serializer is copyable:  
https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__serializer/operator_eq_.html

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-20 14:52:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1200#issuecomment-406624746  

Needs "(Deleted)" in the javadoc, or we can use `#ifndef BEAST_DOXYGEN` to remove it from the docs.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-06-18 20:52:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1200#issuecomment-1159560090  

`http::serializer::operator=` is already being rendered as  
  
```cpp  
serializer&  
operator=(  
    serializer const&) = delete;  
```  
  
Is there anything else we need to do here?  
  
Do we want to remove `operator=` completely from the table of `http::serializer` member functions?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-21 23:23:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1200#issuecomment-1162456090  

Looks good
