# #26 fix(info): add support of error_info usage across shared libraries on… [Closed]

> Username: ledocc  
> Created at: 2019-10-28 12:10:39 UTC  
> Updated at: 2020-02-26 20:15:47 UTC  
> Closed at: 2020-02-26 20:15:47 UTC  
> Url: https://github.com/boostorg/exception/pull/26  

… MacOS  
  
On MacOS, with apple-clang, std::type_info is not shared across dynamic  
library, so equality test of std::type_info based on address failed. Use  
std::type_info::name() instead.

---

## Comment 1

> Username: ledocc  
> Created_at: 2020-01-24 14:45:07 UTC  
> Url: https://github.com/boostorg/exception/pull/26#issuecomment-578159290  

up  
cloning_test fail with clang 3.6 and 3.7, but this no related to my change, develop branch also fail

---

## Comment 2

> Username: zajo  
> Created_at: 2020-02-20 22:10:19 UTC  
> Url: https://github.com/boostorg/exception/pull/26#issuecomment-589372149  

This is not a good idea, two different binaries may implement two different (hidden) types using the same name, which would now be treated as one.

---
