# #130 - char8_t overloads not implemented [Open]

> Username: rolandreichwein  
> Created at: 2021-12-07 11:53:30 UTC  
> Updated at: 2021-12-07 19:05:06 UTC  
> Url: https://github.com/boostorg/range/issues/130  

In include/boost/range/as_literal.hpp, the C++20 char8_t overloads are not implemented, in contrast to the already existing char16_t and char32_t versions. This leads to e.g.  
  
boost::algorithm::starts_with(u8".git", u8".") == false  
  
but only for C++20, and with u8, resp. char8_t.

---

## Comment 1

> Username: rolandreichwein  
> Created at: 2021-12-07 19:05:02 UTC  
> Url: https://github.com/boostorg/range/issues/130#issuecomment-988192262  

Please consider the attached patch that fixes it for me.  
(.zip since interface didn't accept .patch directly)  
  
[boost-range.zip](https://github.com/boostorg/range/files/7670908/boost-range.zip)
