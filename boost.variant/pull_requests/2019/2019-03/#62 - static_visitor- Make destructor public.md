# #62 static_visitor: Make destructor public [Merged]

> Username: gjasny  
> Created at: 2019-03-09 14:24:34 UTC  
> Updated at: 2019-03-16 15:01:05 UTC  
> Merged at: 2019-03-16 14:59:29 UTC  
> Closed at: 2019-03-16 14:59:29 UTC  
> Url: https://github.com/boostorg/variant/pull/62  

otherwise in C++ 17 mode Clang 8 will complain about the  
protected destructor:  
  
```  
main.cpp:16:33: error: temporary of type 'boost::static_visitor<>' has protected destructor  
    boost::apply_visitor(output{}, v);  
                                ^  
/usr/local/opt/boost/include/boost/variant/static_visitor.hpp:53:5: note: declared protected here  
    ~static_visitor() = default;  
    ^  
```  
  
See also discussion in https://reviews.llvm.org/D53860

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-03-09 14:51:38 UTC  
> Updated_at: 2019-03-09 16:15:39 UTC  
> Url: https://github.com/boostorg/variant/pull/62#issuecomment-471185337  

[![Coverage Status](https://coveralls.io/builds/22084079/badge)](https://coveralls.io/builds/22084079)  
  
Coverage remained the same at 95.063% when pulling **dd728220b00772cee5d3aea5850a2bb09eecc66b on gjasny:static-visitor-public-dtor** into **d069511e31a3b7fee45e22634bb43b8f4e4608cd on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-03-09 20:49:43 UTC  
> Url: https://github.com/boostorg/variant/pull/62#issuecomment-471220695  

I'll double check the intent of that C++17 change in the WG21. If there are reasonable benefits from that aggregate behavior change then I'll merge. Otherwise this is going to be a C++ core issue.

---

## Comment 3

> Username: gjasny  
> Created_at: 2019-03-09 20:57:54 UTC  
> Url: https://github.com/boostorg/variant/pull/62#issuecomment-471221308  

Thanks for taking care. Would there be a downside if the destructor is public?

---

## Comment 4

> Username: apolukhin  
> Created_at: 2019-03-16 15:00:48 UTC  
> Url: https://github.com/boostorg/variant/pull/62#issuecomment-473538978  

No downside for `static_visitor`, but I'm worried about all the code bases where `= default;` is used.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2019-03-16 15:01:05 UTC  
> Url: https://github.com/boostorg/variant/pull/62#issuecomment-473538996  

Many thanks for the report and for the PR.

---
