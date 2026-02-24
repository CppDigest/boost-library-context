# #197 - `match_results` has different parameter type compared to `std::match_results` [Closed]

> Username: zufuliu  
> Created at: 2023-10-09 22:36:34 UTC  
> Updated at: 2024-03-25 17:42:44 UTC  
> Closed at: 2024-03-25 17:42:44 UTC  
> Url: https://github.com/boostorg/regex/issues/197  

this cause warnings when porting `std::regex` code to `boost::regex`: `warning C4267: 'argument': conversion from 'size_t' to 'int', possible loss of data`.  
  
`difference_type length(int sub = 0) const`  
https://github.com/boostorg/regex/blob/develop/include/boost/regex/v5/match_results.hpp#L100  
https://en.cppreference.com/w/cpp/regex/match_results/length  
  
`string_type str(int sub = 0) const`  
https://github.com/boostorg/regex/blob/develop/include/boost/regex/v5/match_results.hpp#L164  
https://en.cppreference.com/w/cpp/regex/match_results/str  
  
`const_reference operator[](int sub) const`  
https://github.com/boostorg/regex/blob/develop/include/boost/regex/v5/match_results.hpp#L199  
https://en.cppreference.com/w/cpp/regex/match_results/operator_at  
  
I think it's better to use `size_type sub` as parameter as `difference_type position(size_type sub = 0) const`:  
https://github.com/boostorg/regex/blob/develop/include/boost/regex/v5/match_results.hpp#L131  
https://en.cppreference.com/w/cpp/regex/match_results/position  
  
and C-style cast inside these `int sub` functions can be avoided:  
```diff  
- if(sub < (int)m_subs.size() && (sub > 0))  
+ if(sub < m_subs.size())  
```

---

## Comment 1

> Username: zufuliu  
> Created at: 2023-10-13 00:03:40 UTC  
> Url: https://github.com/boostorg/regex/issues/197#issuecomment-1760556779  

This seem can't be fixed as MSVC can't disambiguates `operator [](const wchar_t *) const` and `operator [](unsigned __int64) const`  
for `search_base = position = m_result[0].second;` inside perl_matcher_common.hpp.
