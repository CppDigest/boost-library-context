# #389 Warnings and small fixes [Closed]

> Username: alandefreitas  
> Created at: 2022-08-12 03:54:47 UTC  
> Updated at: 2022-08-30 21:04:13 UTC  
> Closed at: 2022-08-12 22:42:16 UTC  
> Url: https://github.com/boostorg/url/pull/389  

fix #377, fix #379, fix #386, fix #387

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-12 03:56:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/389#pullrequestreview-1070569735  

📁 doc/qbk/5.1.CharSet.qbk

```diff
  15 | ```
  16 |- bool( char ch ) const;
  16 |+ bool( char ch ) const noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-08-12 03:56:20 UTC  
> Url: https://github.com/boostorg/url/pull/389#discussion_r944095002  

But you need to go through all the charsets provided by the library (e.g. `lut_chars`) and make sure they are noexcept. And you should make sure that the documentation does not imply the `constexpr` requirement on `bool operator()( char )`. In fact we should remove the `constexpr` designation from our signatures of that function.

> Username: alandefreitas  
> Created_at: 2022-08-12 06:37:11 UTC  
> Url: https://github.com/boostorg/url/pull/389#discussion_r944155694  

Yes. I did check that. The examplar in snippets.cpp has no constexpr. I'll check the other existing functions.


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-12 04:00:20 UTC  
> Url: https://github.com/boostorg/url/pull/389#issuecomment-1212706393  

An automated preview of the documentation is available at [https://389.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://389.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-12 04:03:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/389#pullrequestreview-1070572594  

📁 include/boost/url/url.hpp

```diff
 269 |+     @li If none of the conditions above apply,
 270 |+         any base URL depends the context of the
 271 |+         application
```

> Username: vinniefalco  
> Created_at: 2022-08-12 04:03:40 UTC  
> Url: https://github.com/boostorg/url/pull/389#discussion_r944097076  

...the hell? I can't understand anything

> Username: alandefreitas  
> Created_at: 2022-08-12 06:37:42 UTC  
> Url: https://github.com/boostorg/url/pull/389#discussion_r944155961  

I'll simplify that then


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-12 04:04:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/389#pullrequestreview-1070572889  

📁 include/boost/url/url.hpp

```diff
 290 |+     @par Example
 291 |+     @code
 292 |+     url out;
```

> Username: vinniefalco  
> Created_at: 2022-08-12 04:04:24 UTC  
> Updated_at: 2022-08-12 04:04:25 UTC  
> Url: https://github.com/boostorg/url/pull/389#discussion_r944097300  

```  
url dest;  
```


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-12 04:06:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/389#pullrequestreview-1070573615  

📁 test/unit/snippets.cpp

```diff
  35 |+         //[snippet_accessing_1
  36 |+         string_view s = "https://user:pass@example.com:443/path/to/my%2dfile.txt?id=42&name=John%20Doe+Jingleheimer%2DSchmidt#page%20anchor";
  37 |+         url_view u( s );
```

> Username: vinniefalco  
> Created_at: 2022-08-12 04:06:12 UTC  
> Url: https://github.com/boostorg/url/pull/389#discussion_r944097751  

why do we need `s` ?


---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2022-08-12 04:09:52 UTC  
> Updated_at: 2022-08-12 22:32:41 UTC  
> Url: https://github.com/boostorg/url/pull/389#issuecomment-1212710227  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/389?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#389](https://codecov.io/gh/CPPAlliance/url/pull/389?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (89eb52c) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/0ea156c9ce8927bca979b09ec66f089cf0133248?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (0ea156c) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 89eb52c differs from pull request most recent head b794bd1. Consider uploading reports for the commit b794bd1 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/389/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/389?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #389   +/-   ##  
========================================  
  Coverage    97.89%   97.89%             
========================================  
  Files          131      131             
  Lines         6267     6267             
========================================  
  Hits          6135     6135             
  Misses         132      132             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/389?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_encoded.hpp](https://codecov.io/gh/CPPAlliance/url/pull/389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_encoded.ipp](https://codecov.io/gh/CPPAlliance/url/pull/389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZC5pcHA=) | `97.36% <ø> (ø)` | |  
| [include/boost/url/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/CPPAlliance/url/pull/389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/CPPAlliance/url/pull/389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/389?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/389?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [0ea156c...b794bd1](https://codecov.io/gh/CPPAlliance/url/pull/389?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-08-12 20:55:22 UTC  
> Url: https://github.com/boostorg/url/pull/389#issuecomment-1213513734  

An automated preview of the documentation is available at [https://389.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://389.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-08-12 21:25:22 UTC  
> Url: https://github.com/boostorg/url/pull/389#issuecomment-1213531738  

An automated preview of the documentation is available at [https://389.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://389.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-12 21:35:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/389#pullrequestreview-1071796339  

📁 test/unit/snippets.cpp

```diff
 273 |     params p = u.params();
 274 |-     p.emplace_at(p.find("name"), "name", "Vinnie Falco");
 274 |+     p.replace(p.find("name"), "name", "Vinnie Falco");
```

> Username: vinniefalco  
> Created_at: 2022-08-12 21:35:48 UTC  
> Url: https://github.com/boostorg/url/pull/389#discussion_r944876924  

Don't use my name, LOL.. Dont use real names. Use "John Doe" and "Jane Doe."

> Username: alandefreitas  
> Created_at: 2022-08-12 22:30:53 UTC  
> Url: https://github.com/boostorg/url/pull/389#discussion_r944909485  

LOL... I don't even remember when that was included.   
  
Yes. It sounds narcissistic. :smile:


---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-08-12 22:40:20 UTC  
> Url: https://github.com/boostorg/url/pull/389#issuecomment-1213570311  

An automated preview of the documentation is available at [https://389.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://389.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 11

> Username: alandefreitas  
> Created_at: 2022-08-12 22:42:16 UTC  
> Url: https://github.com/boostorg/url/pull/389#issuecomment-1213570983  

Merged

---
