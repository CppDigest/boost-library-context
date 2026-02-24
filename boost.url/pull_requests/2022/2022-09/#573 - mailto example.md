# #573 mailto example [Closed]

> Username: alandefreitas  
> Created at: 2022-09-28 00:57:14 UTC  
> Updated at: 2022-10-13 04:49:38 UTC  
> Closed at: 2022-10-03 22:44:13 UTC  
> Url: https://github.com/boostorg/url/pull/573  

fix #449

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-09-28 01:00:28 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1260259797  

Another example that became much harder than expected after actually reading the protocol. In any case, it's a nice example.  
  
The RFC expects `%20` in params (`%20` is ` ` and `+` is actually `+`). See the examples https://www.rfc-editor.org/rfc/rfc6068  
I don't think the API provides a way to do that, even if we use the encoded query, because `set_query` would already convert ` ` to `+`.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-09-28 01:09:25 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1260264992  

GCOVR code coverage report [https://573.url.prtest.cppalliance.org/gcovr/index.html](https://573.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://573.url.prtest.cppalliance.org/genhtml/index.html](https://573.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-09-28 01:12:34 UTC  
> Updated_at: 2022-10-03 22:43:47 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1260266675  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/573?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#573](https://codecov.io/gh/boostorg/url/pull/573?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b7fff0b) into [develop](https://codecov.io/gh/boostorg/url/commit/3af2fad5f9d17f188544387b69904ff4772a1b16?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3af2fad) will **decrease** coverage by `0.04%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head b7fff0b differs from pull request most recent head c40b545. Consider uploading reports for the commit c40b545 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/573/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/573?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #573      +/-   ##  
===========================================  
- Coverage    96.81%   96.76%   -0.05%       
===========================================  
  Files          138      138                
  Lines         6678     6700      +22       
===========================================  
+ Hits          6465     6483      +18       
- Misses         213      217       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/573?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/573/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.70% <0.00%> (-0.33%)` | :arrow_down: |  
| [include/boost/url/grammar/string\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/573/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9zdHJpbmdfdmlld19iYXNlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/573?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/573?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3af2fad...c40b545](https://codecov.io/gh/boostorg/url/pull/573?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-09-28 02:26:05 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1260306125  

so `set_query` needs `encode_opts`?

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-09-28 04:47:32 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1260383294  

> so set_query needs encode_opts?  
  
I'm not sure what the best design would be. Probably. Yes. This also implies we would need `params` to carry its own `decode_opts`.  
  
I think we are often tending towards treating the general syntax `url_view` as if it were some kind of scheme-based `http_url_view` class.   
  
And that works 99% of the time. It's also great because it's the most common scheme by far.   
  
But it also creates conflicts when the `url_view` is really being used as the general syntax for any other scheme.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-09-28 15:04:31 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1261050466  

A goal of the library is to be agnostic to the scheme. Yes we have separate containers that address the most common hierarchical scheme but it should still work well for any scheme. This sounds like a design flaw.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-09-28 15:05:33 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1261051991  

I think having `decode_opts` in params is fine (and giving the user a way to get and set it)

---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:42:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1124499235  

📁 example/mailto/mailto.cpp

```diff
 312 |+     static
 313 |+     bool
 314 |+     is_atext(char c) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:42:50 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982968014  

should this be expressed as a _CharSet_ ?

> Username: alandefreitas  
> Created_at: 2022-09-29 00:46:31 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982969290  

Technically, all these private functions could be expressed as rules and charsets. I just thought that would be very long but I can experiment with it.

> Username: vinniefalco  
> Created_at: 2022-09-29 04:15:38 UTC  
> Updated_at: 2022-09-29 04:15:39 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r983047882  

I would hope it would be comparable, but even if it is longer, if we are parsing something we should use the idioms offered by the library...


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:43:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1124499628  

📁 example/mailto/mailto.cpp

```diff
 329 |+     static
 330 |+     bool
 331 |+     is_quoted_string(urls::string_view s) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:43:41 UTC  
> Updated_at: 2022-09-29 00:44:09 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982968308  

shouldn't this be expressed as a grammar rule? e.g. `quoted_string_rule`, even if it is in an implementation detail?

> Username: alandefreitas  
> Created_at: 2022-09-29 00:47:44 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982969814  

Same as https://github.com/boostorg/url/pull/573#discussion_r982969290


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:45:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1124500306  

📁 example/mailto/mailto.cpp

```diff
 373 |+             it = it0;
 374 |+         }
 375 |+         // qcontent = qtext / quoted-pair
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:45:02 UTC  
> Updated_at: 2022-09-29 00:45:03 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982968785  

HTTP use quoted-pair as well... in rfc7230: https://www.rfc-editor.org/rfc/rfc7230#section-3.2.6

> Username: alandefreitas  
> Created_at: 2022-09-29 00:46:59 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982969486  

nice

> Username: alandefreitas  
> Created_at: 2022-09-29 00:47:18 UTC  
> Updated_at: 2022-09-29 00:47:19 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982969607  

:laughing:


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:45:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1124500585  

📁 example/mailto/mailto.cpp

```diff
 379 |+ 
 380 |+     static
 381 |+     bool parse_qcontent(char const*& it, char const* end) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:45:40 UTC  
> Updated_at: 2022-09-29 00:45:41 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982969016  

LOL...  
https://github.com/CPPAlliance/http_proto/blob/f2382d8eab8be2e9d6e6e14c5502d90ccf55e95f/include/boost/http_proto/rfc/quoted_token_rule.hpp#L90


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:46:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1124500828  

📁 example/mailto/mailto.cpp

```diff
 504 |+     static
 505 |+     bool
 506 |+     is_obs_ctext(char c) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:46:10 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982969165  

_CharSet_ ?


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:46:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1124501182  

📁 example/mailto/mailto.cpp

```diff
 708 |+             k != "cc" &&
 709 |+             k != "body" &&
 710 |+             k != "in-reply-to")
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:46:49 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982969409  

Should this be case-insensitive?


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:47:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1124501399  

📁 example/mailto/mailto.cpp

```diff
 698 |+     static
 699 |+     bool
 700 |+     is_header(urls::params_encoded_view::reference p) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:47:16 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982969590  

wouldn't it be better to use `urls::param_pct_view qp`?


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:47:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1124501575  

📁 example/mailto/mailto.cpp

```diff
 719 |+ 
 720 |+     This is a more convenient user-facing function
 721 |+     to parse magnet links.
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:47:38 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r982969761  

magnet? :)


---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-09-29 20:38:19 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1262788841  

GCOVR code coverage report [https://573.url.prtest.cppalliance.org/gcovr/index.html](https://573.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://573.url.prtest.cppalliance.org/genhtml/index.html](https://573.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-09-29 22:46:56 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1262909257  

An automated preview of the documentation is available at [https://573.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://573.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-09-29 22:48:22 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1262909999  

GCOVR code coverage report [https://573.url.prtest.cppalliance.org/gcovr/index.html](https://573.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://573.url.prtest.cppalliance.org/genhtml/index.html](https://573.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 22:56:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126081159  

📁 example/mailto/mailto.cpp

```diff
  58 |+     @li <a href="https://www.rfc-editor.org/rfc/rfc2368"
  59 |+         >The mailto URL scheme</a>
  60 |+     @li <a href="https://www.rfc-editor.org/rfc/rfc1738"
```

> Username: vinniefalco  
> Created_at: 2022-09-29 22:56:15 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984081408  

maybe you should list the errata URL as well :)  
  
do we need the older RFC?

> Username: alandefreitas  
> Created_at: 2022-09-29 23:21:40 UTC  
> Updated_at: 2022-09-29 23:30:04 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984091516  

> maybe you should list the errata URL as well :)  
  
Sure.  
  
> do we need the older RFC?  
  
No... We don't _need_ it


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 22:59:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126082744  

📁 example/mailto/mailto.cpp

```diff
  95 |+     {
  96 |+         // Look for ith email address in the path string
  97 |+         auto s = ith_address(u_.encoded_path(), idx);
```

> Username: vinniefalco  
> Created_at: 2022-09-29 22:59:07 UTC  
> Updated_at: 2022-09-29 22:59:08 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984082696  

I'm not fond of "ith_address", it looks like a misspelling e.g. "with_address"


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 22:59:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126082833  

📁 example/mailto/mailto.cpp

```diff
  90 |+     }
  91 |+ 
  92 |+     /// @copydoc address()
```

> Username: vinniefalco  
> Created_at: 2022-09-29 22:59:19 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984082747  

copydoc but we don't run this through doxygen so what's the point of that?

> Username: alandefreitas  
> Created_at: 2022-09-30 01:01:08 UTC  
> Updated_at: 2022-09-30 01:01:09 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984126660  

I don't know. When I wrote the magnet example, you told me to include javadocs, and I've been doing it since then.

> Username: vinniefalco  
> Created_at: 2022-10-03 22:53:52 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r986299100  

what I mean when I say "include the javadocs" in the examples is, provide sufficient commentary so the user can understand the code


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:00:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126083300  

📁 example/mailto/mailto.cpp

```diff
 143 |+      */
 144 |+     std::string
 145 |+     cc(std::size_t idx) const
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:00:08 UTC  
> Updated_at: 2022-09-29 23:00:26 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984083103  

Can we just say `i` or `index` and not use the abbreviation which is unpronounceable? (except for "iddix" which sounds terrible lol)


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:08:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126087850  

📁 example/mailto/mailto.cpp

```diff
 157 |+         while (it != ps.end())
 158 |+         {
 159 |+             auto s = ith_address((*it++).value, idx);
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:08:29 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984086530  

how about `get_nth_address`?

> Username: alandefreitas  
> Created_at: 2022-09-30 01:01:56 UTC  
> Updated_at: 2022-09-30 01:02:12 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984126882  

I was thinking of `address_at` but that's better


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:09:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126088236  

📁 example/mailto/mailto.cpp

```diff
 266 |+ 
 267 |+     urls::pct_string_view
 268 |+     param_or_empty(urls::string_view k) const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:09:13 UTC  
> Updated_at: 2022-09-29 23:09:14 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984086831  

This is like `value_or`?

> Username: vinniefalco  
> Created_at: 2022-09-29 23:22:18 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984091740  

The function can't be `noexcept` because the conversion of `k` to `pct_string_view` can throw (if the encoding is invalid)


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:23:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126095470  

📁 example/mailto/mailto.cpp

```diff
 114 |+     /// Return number of email addresses in the URL
 115 |+     std::size_t
 116 |+     size() const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:23:50 UTC  
> Updated_at: 2022-09-29 23:23:51 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984092352  

We need to separate all the function definitions from the declarations, because otherwise there is no good way to look at the synopsis for the class to get a feel for its API. Sorry!


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:24:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126095807  

📁 example/mailto/mailto_grammar.hpp

```diff
  17 |+ #include <boost/url/grammar/delim_rule.hpp>
  18 |+ #include <boost/url/grammar/vchars.hpp>
  19 |+ #include <boost/url/grammar/ci_string.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:24:29 UTC  
> Updated_at: 2022-09-29 23:24:30 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984092628  

These are not alphabetized


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:24:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126095954  

📁 example/mailto/mailto_grammar.hpp

```diff
  25 |+ struct dtext_no_obs_chars_t
  26 |+ {
  27 |+     constexpr dtext_no_obs_chars_t() noexcept = default;
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:24:46 UTC  
> Updated_at: 2022-09-29 23:24:47 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984092726  

I don't think you need this?


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:25:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126096257  

📁 example/mailto/mailto_grammar.hpp

```diff
  80 |+ 
  81 |+ /// A character set containing atext characters.
  82 |+ constexpr atext_chars_t atext_chars{};
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:25:24 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984093006  

why not use `lut_chars`?

> Username: vinniefalco  
> Created_at: 2022-09-29 23:26:17 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984093338  

```  
constexpr auto atext_chars = grammar::alpha_chars + grammar::digit_chars + "!#$%&'*+-/=?^_`{|}~";

> Username: alandefreitas  
> Created_at: 2022-09-30 01:11:45 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984130175  

```  
error: no match for ‘operator+’ (operand types are ‘const boost::urls::grammar::alnum_chars_t’ and ‘const boost::urls::grammar::digit_chars_t’)  
```  
  
:man_shrugging:   
  
```  
constexpr auto atext_chars =  
    boost::urls::grammar::alnum_chars +  
    boost::urls::grammar::digit_chars +  
    "!#$%&'*+-/=?^_`{|}~";  
```

> Username: alandefreitas  
> Created_at: 2022-09-30 01:12:26 UTC  
> Updated_at: 2022-09-30 01:12:27 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984130396  

```  
constexpr auto atext_chars =  
    boost::urls::grammar::lut_chars("!#$%&'*+-/=?^_`{|}~") +  
    boost::urls::grammar::alnum_chars +  
    boost::urls::grammar::digit_chars;  
```  
  
works though


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:27:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126097217  

📁 example/mailto/mailto_grammar.hpp

```diff
  68 |+         // "*" / "+" / "-" / "/" / "=" / "?" / "^" / "_" / "`" / "{" /
  69 |+         // "|" / "}" / "~"
  70 |+         return boost::urls::grammar::alnum_chars(c) ||
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:27:25 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984093790  

maybe we could make a few global aliases?  
```  
namespace urls = boost::urls;  
namespace grammar = boost::urls::grammar;  
```


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:28:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126097527  

📁 example/mailto/mailto_grammar.hpp

```diff
  91 |+     operator()(char c) const noexcept
  92 |+     {
  93 |+         return c == ' ';
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:28:02 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984094048  

What about HTAB?


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:28:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126097774  

📁 example/mailto/mailto_grammar.hpp

```diff
  99 |+ 
 100 |+ /// The set of obs_ctext characters
 101 |+ struct obs_ctext_chars_t
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:28:32 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984094224  

this could be a `lut_chars`

> Username: alandefreitas  
> Created_at: 2022-09-30 01:20:37 UTC  
> Updated_at: 2022-09-30 01:20:44 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984132863  

I wasn't sure about this one. How do we do that with lut_chars?


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:28:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126097871  

📁 example/mailto/mailto_grammar.hpp

```diff
 132 |+ 
 133 |+ /// The set of obs_qtext characters
 134 |+ struct obs_qtext_chars_t
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:28:43 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984094285  

same here, `lut_chars`


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:28:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126097960  

📁 example/mailto/mailto_grammar.hpp

```diff
 148 |+     operator()(char c) const noexcept
 149 |+     {
 150 |+         return obs_ctext_chars(c);
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:28:54 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984094353  

this doesn't look right?


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 23:32:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/573#pullrequestreview-1126099772  

📁 example/mailto/mailto_grammar.hpp

```diff
 455 |+                    std::begin(valid_k),
 456 |+                    std::end(valid_k),
 457 |+                    match))
```

> Username: vinniefalco  
> Created_at: 2022-09-29 23:32:33 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984095859  

What's the point of having lambdas if you are not going to put them in the argument list?

> Username: alandefreitas  
> Created_at: 2022-09-30 01:22:33 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r984133452  

What?

> Username: vinniefalco  
> Created_at: 2022-10-03 20:46:08 UTC  
> Updated_at: 2022-10-03 20:46:09 UTC  
> Url: https://github.com/boostorg/url/pull/573#discussion_r986214181  

Why not  
```  
    if( std::any_of( std::begin(valid_k), std::end(valid_k),  
        [s]( urls::string_view valid_k )  
        {  
            return grammar::ci_is_equal( s, valid_k );  
        };);  
```  
?


---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2022-09-30 01:31:56 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1263001871  

An automated preview of the documentation is available at [https://573.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://573.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2022-09-30 01:34:43 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1263003459  

GCOVR code coverage report [https://573.url.prtest.cppalliance.org/gcovr/index.html](https://573.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://573.url.prtest.cppalliance.org/genhtml/index.html](https://573.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 37

> Username: alandefreitas  
> Created_at: 2022-09-30 19:13:36 UTC  
> Updated_at: 2022-09-30 19:14:21 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1263926500  

Any ideas to improve that recursive rule?  
  
I tried to get some inspiration from the spirit example but it wasn't very helpful in our case.  
  
https://www.boost.org/doc/libs/1_80_0/libs/spirit/doc/html/spirit/qi/tutorials/mini_xml___asts_.html

---

## Comment 38

> Username: vinniefalco  
> Created_at: 2022-10-03 22:19:04 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1266127395  

you can merge this now, and also improve it later if you want

---

## Comment 39

> Username: alandefreitas  
> Created_at: 2022-10-03 22:44:13 UTC  
> Url: https://github.com/boostorg/url/pull/573#issuecomment-1266150684  

Merged

---
