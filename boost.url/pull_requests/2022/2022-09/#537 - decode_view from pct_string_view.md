# #537 decode_view from pct_string_view [Closed]

> Username: alandefreitas  
> Created at: 2022-09-12 20:28:18 UTC  
> Updated at: 2022-09-19 21:30:18 UTC  
> Closed at: 2022-09-14 17:52:17 UTC  
> Url: https://github.com/boostorg/url/pull/537  

fix #534

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-12 20:30:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/537#pullrequestreview-1104796416  

📁 include/boost/url/decode_view.hpp

```diff
1019 |+ inline
1020 |+ decode_view
1021 |+ operator*(pct_string_view s) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-12 20:30:38 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r968904022  

IF you do this then the operator will just be by itself in the docs and not attached to `pct_string_view` where it should be...


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-12 20:31:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/537#pullrequestreview-1104797736  

📁 include/boost/url/pct_string_view.hpp

```diff
 197 |+     template <class Allocator>
 198 |+     pct_string_view(
 199 |+         std::basic_string<char, Allocator> const& s)
```

> Username: vinniefalco  
> Created_at: 2022-09-12 20:31:51 UTC  
> Updated_at: 2022-09-12 20:32:01 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r968904909  

something ain't right here... if you are going to add a templated constructor then shouldn't it be anything which is convertible to `urls::string_view`? What code needs this?

> Username: alandefreitas  
> Created_at: 2022-09-12 21:09:01 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r968932823  

> shouldn't it be anything which is convertible to urls::string_view  
  
That became ambiguous now. For the same reason, we need the `pct_string_view(char const* s)` just above this one.

> Username: vinniefalco  
> Created_at: 2022-09-13 18:58:36 UTC  
> Updated_at: 2022-09-13 18:58:50 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r969985006  

why not  
```  
template<  
    class StringLike  
    , class = typename std::enable_if<  
        std::is_convertible<  
            StringLike,  
            string_view  
                >::value>:: type  
>  
pct_string_view( StringLike const& );  
```


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-12 20:32:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/537#pullrequestreview-1104798290  

📁 test/unit/rfc/query_rule.cpp

```diff
  12 | 
  13 | #include <boost/url/grammar/range_rule.hpp>
  14 |+ #include <boost/url/decode_view.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-09-12 20:32:23 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r968905296  

these includes are not in the right order


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-12 21:56:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/537#pullrequestreview-1104882307  

📁 include/boost/url/decode_view.hpp

```diff
 183 | 
 184 |+         @throw std::invalid_argument `s` is
 185 |+         not a valid percent-encoded string.
```

> Username: vinniefalco  
> Created_at: 2022-09-12 21:56:08 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r968967332  

Wrong exception


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-12 22:15:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/537#pullrequestreview-1104898720  

📁 include/boost/url/decode_view.hpp

```diff
 195 |-         string_view s,
 196 |-         decode_opts opt = {});
 196 |+         pct_string_view s,
```

> Username: vinniefalco  
> Created_at: 2022-09-12 22:15:28 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r968979908  

Where's the macro?


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-13 13:36:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/537#pullrequestreview-1105817952  

📁 include/boost/url/detail/config.hpp

```diff
 112 | 
 113 |+ #if defined(BOOST_URL_DOCS)
 114 |+ #define BOOST_URL_PCT_STRING_VIEW string_view
```

> Username: vinniefalco  
> Created_at: 2022-09-13 13:36:09 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r969638524  

I think you have to do this in the Jamfile, not like this

> Username: alandefreitas  
> Created_at: 2022-09-13 17:42:35 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r969914552  

Good idea. Done.


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-09-13 17:51:45 UTC  
> Url: https://github.com/boostorg/url/pull/537#issuecomment-1245755693  

An automated preview of the documentation is available at [https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-09-13 20:21:46 UTC  
> Url: https://github.com/boostorg/url/pull/537#issuecomment-1245915968  

An automated preview of the documentation is available at [https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-13 20:30:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/537#pullrequestreview-1106413931  

📁 include/boost/url/pct_string_view.hpp

```diff
 198 |-     template <class Allocator>
 171 |+     template<
 172 |+         class StringLike
```

> Username: vinniefalco  
> Created_at: 2022-09-13 20:30:56 UTC  
> Updated_at: 2022-09-13 20:30:57 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r970056330  

this actually worked??

> Username: alandefreitas  
> Created_at: 2022-09-13 20:40:01 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r970062993  

It worked on GCC, Clang, MSVC, C++11, and C++20 locally. Let's see what CI says. :)


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-14 15:28:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/537#pullrequestreview-1107748322  

📁 include/boost/url/pct_string_view.hpp

```diff
 198 |-     template <class Allocator>
 171 |+     template<
 172 |+         class StringLike
```

> Username: vinniefalco  
> Created_at: 2022-09-14 15:28:18 UTC  
> Updated_at: 2022-09-14 15:28:19 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r970974782  

Lets call it "String" so users don't get confused... I copied from cppreference which probably wasn't the best.

> Username: alandefreitas  
> Created_at: 2022-09-14 16:14:25 UTC  
> Url: https://github.com/boostorg/url/pull/537#discussion_r971033324  

Done


---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-09-14 16:21:48 UTC  
> Url: https://github.com/boostorg/url/pull/537#issuecomment-1247009613  

An automated preview of the documentation is available at [https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-09-14 16:36:50 UTC  
> Url: https://github.com/boostorg/url/pull/537#issuecomment-1247028323  

An automated preview of the documentation is available at [https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 13

> Username: codecov[bot]  
> Created_at: 2022-09-14 16:42:46 UTC  
> Updated_at: 2022-09-14 16:50:08 UTC  
> Url: https://github.com/boostorg/url/pull/537#issuecomment-1247035404  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/537?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#537](https://codecov.io/gh/boostorg/url/pull/537?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (db8ceea) into [develop](https://codecov.io/gh/boostorg/url/commit/5ff23a35045fe948cfad8f305aef63a8b785bcac?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5ff23a3) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head db8ceea differs from pull request most recent head 84e5481. Consider uploading reports for the commit 84e5481 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/537/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/537?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #537      +/-   ##  
===========================================  
- Coverage    96.68%   96.68%   -0.01%       
===========================================  
  Files          137      137                
  Lines         6586     6577       -9       
===========================================  
- Hits          6368     6359       -9       
  Misses         218      218                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/537?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/decode\_view.ipp](https://codecov.io/gh/boostorg/url/pull/537/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9kZWNvZGVfdmlldy5pcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/pct\_string\_view.hpp](https://codecov.io/gh/boostorg/url/pull/537/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X3N0cmluZ192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/decode\_view.hpp](https://codecov.io/gh/boostorg/url/pull/537/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlX3ZpZXcuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/537?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/537?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5ff23a3...84e5481](https://codecov.io/gh/boostorg/url/pull/537?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-09-14 16:56:51 UTC  
> Url: https://github.com/boostorg/url/pull/537#issuecomment-1247051509  

An automated preview of the documentation is available at [https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://537.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
