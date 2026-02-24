# #40 [clang] Fix [-Wc++11-narrowing] error [Closed]

> Username: hia3  
> Created at: 2017-09-19 19:55:13 UTC  
> Updated at: 2017-09-19 21:10:03 UTC  
> Closed at: 2017-09-19 21:10:03 UTC  
> Url: https://github.com/boostorg/log/pull/40  

boost/log/attributes/attribute_name.hpp:56:28: error: enumerator value evaluates to 4294967295, which cannot be narrowed to type 'int' [-Wc++11-narrowing]  
    enum { uninitialized = 0xFFFFFFFFu };  
                           ^

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-19 21:10:03 UTC  
> Url: https://github.com/boostorg/log/pull/40#issuecomment-330674466  

Should be fixed in https://github.com/boostorg/log/commit/f42c2a8dede4a1c60bea7ec0e2eda8e202b553c6. Thanks.

---
