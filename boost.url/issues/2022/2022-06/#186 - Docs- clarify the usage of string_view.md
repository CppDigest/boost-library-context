# #186 - Docs: clarify the usage of string_view [Closed]

> Username: akrzemi1  
> Created at: 2022-06-06 06:42:58 UTC  
> Updated at: 2022-06-09 20:07:22 UTC  
> Closed at: 2022-06-09 20:07:22 UTC  
> Url: https://github.com/boostorg/url/issues/186  

Given the unclear situation in Boost regarding the `string_view`, I think the docs should say that the library uses alias `boost::urls::string_view`, that this alias:  
  
- Is a custom implementation of `std::string_view`'s interface, which works in C++11.  
- Is convertible to/from `std::string_view`  
- Is an implementation detail of Boost.URL.  
- That the namespace and the name of the aliased class may change without notice.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-07 01:33:29 UTC  
> Url: https://github.com/boostorg/url/issues/186#issuecomment-1148098349  

See: https://github.com/CPPAlliance/url/issues/190#issuecomment-1148006983
