# #116 assume char could be unsigned [Closed]

> Username: alandefreitas  
> Created at: 2022-01-24 21:17:47 UTC  
> Updated at: 2022-03-14 15:56:41 UTC  
> Closed at: 2022-02-02 17:31:59 UTC  
> Url: https://github.com/boostorg/url/pull/116  

Fixes errors that happen with `char` is unsigned.  
  
MSVC: `/J`  
GCC: `-funsigned-char`

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-24 21:20:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/116#pullrequestreview-861529470  

📁 test/unit/pct_encoding.cpp

```diff
 363 |             []( string_view s,
 364 |                 string_view m0,
 365 |-                 bool space_to_plus = false)
```

> Username: vinniefalco  
> Created_at: 2022-01-24 21:20:19 UTC  
> Url: https://github.com/boostorg/url/pull/116#discussion_r791159600  

why change this?


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-24 21:20:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/116#pullrequestreview-861529685  

📁 include/boost/url/authority_view.hpp

```diff
 114 | 
 115 |     // return offset of id
 116 |-     BOOST_URL_CONSTEXPR
```

> Username: vinniefalco  
> Created_at: 2022-01-24 21:20:34 UTC  
> Updated_at: 2022-01-24 21:20:35 UTC  
> Url: https://github.com/boostorg/url/pull/116#discussion_r791159749  

This needs to go in a separate commit


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-01-28 22:41:57 UTC  
> Updated_at: 2022-02-01 05:10:45 UTC  
> Url: https://github.com/boostorg/url/pull/116#issuecomment-1024718427  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/116?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#116](https://codecov.io/gh/CPPAlliance/url/pull/116?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (905492f) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/0e014e86ac045a43b8cf9da6e7a16228f13cef9b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (0e014e8) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/116/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/116?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #116   +/-   ##  
========================================  
  Coverage    96.22%   96.22%             
========================================  
  Files          104      104             
  Lines         5482     5486    +4       
========================================  
+ Hits          5275     5279    +4       
  Misses         207      207             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/116?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/impl/pct\_encoding.ipp](https://codecov.io/gh/CPPAlliance/url/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGN0X2VuY29kaW5nLmlwcA==) | `62.16% <100.00%> (+2.16%)` | :arrow_up: |  
| [include/boost/url/grammar/charset.hpp](https://codecov.io/gh/CPPAlliance/url/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9jaGFyc2V0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/ipv6\_address.ipp](https://codecov.io/gh/CPPAlliance/url/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9pcHY2X2FkZHJlc3MuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/pct\_encoding.hpp](https://codecov.io/gh/CPPAlliance/url/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wY3RfZW5jb2RpbmcuaHBw) | `98.82% <100.00%> (ø)` | |  
| [include/boost/url/impl/pct\_encoding.ipp](https://codecov.io/gh/CPPAlliance/url/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wY3RfZW5jb2RpbmcuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/rfc/impl/pct\_encoded\_rule.hpp](https://codecov.io/gh/CPPAlliance/url/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcGN0X2VuY29kZWRfcnVsZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/116?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/116?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [0e014e8...905492f](https://codecov.io/gh/CPPAlliance/url/pull/116?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-01 01:43:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/116#pullrequestreview-868607314  

📁 include/boost/url/impl/ipv6_address.ipp

```diff
  63 |-                 grammar::hexdig_value(*it);
  64 |-             if(d == -1)
  62 |+             char d{' '};
```

> Username: vinniefalco  
> Created_at: 2022-02-01 01:43:29 UTC  
> Updated_at: 2022-02-01 01:43:30 UTC  
> Url: https://github.com/boostorg/url/pull/116#discussion_r796210599  

why the space?

> Username: alandefreitas  
> Created_at: 2022-02-01 01:53:43 UTC  
> Url: https://github.com/boostorg/url/pull/116#discussion_r796213883  

This is related to the asan error we had before. It was complaining the variable was not being initialized.   
  
I've fixed some other things since then and made `hexdig_value` deterministic so this might not be an error anymore.   
  
I just pushed another commit to check if any compiler still complains about it.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-01 01:57:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/116#pullrequestreview-868612635  

📁 include/boost/url/grammar/charset.hpp

```diff
 490 |+         return true;
 491 |+     }
 492 |+     res = static_cast<char>(0);
```

> Username: vinniefalco  
> Created_at: 2022-02-01 01:57:22 UTC  
> Url: https://github.com/boostorg/url/pull/116#discussion_r796215007  

why are we casting 0? doesn't `res = 0` work?

> Username: alandefreitas  
> Created_at: 2022-02-01 02:03:01 UTC  
> Url: https://github.com/boostorg/url/pull/116#discussion_r796216665  

Removed. I don't even remember why I did that. :confused:


---
