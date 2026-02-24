# #68 Copy variant cvref when determining result type [Merged]

> Username: ecatmur  
> Created at: 2019-04-17 14:04:38 UTC  
> Updated at: 2019-04-23 05:55:14 UTC  
> Merged at: 2019-04-23 05:55:00 UTC  
> Closed at: 2019-04-23 05:55:00 UTC  
> Url: https://github.com/boostorg/variant/pull/68  

Ensures that we can e.g. call apply_visitor on a lvalue variant  
  
e.g. works up to 1.69, broken in 1.70 (broken by #55)  
  
    #include <boost/variant.hpp>  
    int main() {  
        boost::variant<int, long> v;  
        boost::apply_visitor([](auto& i) {}, v);  
    }

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-04-17 14:32:28 UTC  
> Updated_at: 2019-04-21 03:57:16 UTC  
> Url: https://github.com/boostorg/variant/pull/68#issuecomment-484115584  

[![Coverage Status](https://coveralls.io/builds/22932352/badge)](https://coveralls.io/builds/22932352)  
  
Coverage remained the same at 95.297% when pulling **7331d648f46b6008138cbd22087b8c5c61ff7926 on ecatmur:apply-visitor-copy-cv-ref** into **5be655d46f6d87a30bc6d519b03ed499f80a636a on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-04-17 18:19:19 UTC  
> Url: https://github.com/boostorg/variant/pull/68#issuecomment-484204853  

Please also add a testcase

---

## Comment 3

> Username: ecatmur  
> Created_at: 2019-04-20 23:52:07 UTC  
> Url: https://github.com/boostorg/variant/pull/68#issuecomment-485199802  

Added a test. Wasn't quite sure how to write it but this is closest to my motivating use case.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2019-04-23 05:55:13 UTC  
> Url: https://github.com/boostorg/variant/pull/68#issuecomment-485653833  

Many thanks for the fix!

---
