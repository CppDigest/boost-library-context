# #45 - test_composite_key.cpp fails to compile for C++23 [Closed]

> Username: StephanTLavavej  
> Created at: 2021-07-07 23:19:59 UTC  
> Updated at: 2021-07-17 06:45:25 UTC  
> Closed at: 2021-07-17 06:45:24 UTC  
> Url: https://github.com/boostorg/multi_index/issues/45  

I work on MSVC's C++ Standard Library implementation, and we regularly build Boost (along with many other open-source projects) to identify compiler/library bugs that would break your project, so we can fix them before shipping. This also allows us to provide advance notice of source-breaking changes - which is the case here.  
  
The paper [P2166R1](https://wg21.link/P2166R1) "Prohibiting `basic_string` And `basic_string_view` Construction From `nullptr`" has been accepted for the upcoming C++23 Standard, and we recently implemented it by merging https://github.com/microsoft/STL/pull/1995 from our contributor @sam20908. Our open-source test pass then discovered the following code in Boost.MultiIndex's tests that is now disallowed by C++23:  
  
https://github.com/boostorg/multi_index/blob/436c60cd09225f195dd8f381ca16402b043b5c3d/test/test_composite_key.cpp#L329  
  
`std::string str_=0` now fails to compile with:  
  
```  
error C2440: 'default argument': cannot convert from 'int' to 'std::string'  
note: No constructor could take the source type, or constructor overload resolution was ambiguous  
```  
  
(This is because the null pointer constant `0` is convertible to `const char*` and `nullptr_t`, so it's a compiler error, although with a less clear message than `nullptr` would generate.) This code has undefined behavior in C++98 through C++20 (as `string`'s constructor has a precondition that the pointer is non-null); C++23 makes it ill-formed.  
  
There are several ways to fix this code, for example `std::string str_=""`, that are backward-compatible with previous Standards.

---

## Comment 1

> Username: joaquintides  
> Created at: 2021-07-08 17:45:21 UTC  
> Updated at: 2021-07-08 17:53:02 UTC  
> Url: https://github.com/boostorg/multi_index/issues/45#issuecomment-876626639  

Hi STL, thanks for the super detailed report! Fixed in develop, will merge to master in time for Boost 1.77, when the merging window opens (July 15th).

---

## Comment 2

> Username: joaquintides  
> Created at: 2021-07-17 06:45:24 UTC  
> Url: https://github.com/boostorg/multi_index/issues/45#issuecomment-881843920  

Merged to master.
