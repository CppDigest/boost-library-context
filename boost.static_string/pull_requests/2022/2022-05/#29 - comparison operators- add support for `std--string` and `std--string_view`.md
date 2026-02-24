# #29 comparison operators: add support for `std::string` and `std::string_view` [Closed]

> Username: timblechmann  
> Created at: 2022-05-01 03:05:13 UTC  
> Updated at: 2023-01-19 22:58:31 UTC  
> Closed at: 2023-01-19 22:58:31 UTC  
> Url: https://github.com/boostorg/static_string/pull/29  

`static_string` lacks the comparison operators to interact with  
`std::string` and `std::string_view`. this limits its usefulness when  
interacting with these types. e.g. when using a `string_view` to perform  
a lookup in a `std::map<static_string, ...>`

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-01 05:04:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/29#pullrequestreview-958543506  

📁 include/boost/static_string/static_string.hpp

```diff
5681 |+ template<std::size_t N, typename CharT, typename Traits, typename Alloc>
5682 |+ BOOST_STATIC_STRING_CPP14_CONSTEXPR
5683 |+ inline
```

> Username: vinniefalco  
> Created_at: 2022-05-01 05:04:46 UTC  
> Url: https://github.com/boostorg/static_string/pull/29#discussion_r862423509  

function templates are automatically `inline`

> Username: timblechmann  
> Created_at: 2022-05-01 05:28:20 UTC  
> Url: https://github.com/boostorg/static_string/pull/29#discussion_r862425162  

hmm, i was under impression that both template functions and inline functions are emitted as weak symbols, but template functions don't automatically get the inline hint (which the `inline` keyword provides).  
i guess in most cases, the compiler will overrule the inline hint, anyway (though i think i've seen cases where the codegen was actually different)


---

## Comment 2

> Username: timblechmann  
> Created_at: 2022-05-01 05:23:24 UTC  
> Url: https://github.com/boostorg/static_string/pull/29#issuecomment-1114137349  

i've done some more experiments of going through `boost::core::string_view`. there are a few caveats, though:  
* `boost::core::string_view` implicitly assumes `std::char_traits<CharT>`  
* it starts becoming a can of worms when keeping `static_strings::basic_string_view` a `boost::basic_string_view`  
  
it would certainly be a cleaner approach, though

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-05-01 05:27:06 UTC  
> Url: https://github.com/boostorg/static_string/pull/29#issuecomment-1114137792  

char traits is a design flaw in C++

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2023-01-19 22:58:26 UTC  
> Url: https://github.com/boostorg/static_string/pull/29#issuecomment-1397718830  

Obsoleted by #45

---
