# #307 source location option [Closed]

> Username: alandefreitas  
> Created at: 2022-08-02 22:40:21 UTC  
> Updated at: 2022-09-19 21:34:40 UTC  
> Closed at: 2022-09-02 01:09:03 UTC  
> Url: https://github.com/boostorg/url/pull/307  

fix #210

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 22:18:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/307#pullrequestreview-1061071540  

📁 include/boost/url/authority_view.hpp

```diff
 234 |             detail::throw_out_of_range(
 235 |-                 BOOST_CURRENT_LOCATION);
 235 |+                 BOOST_URL_CURRENT_LOCATION);
```

> Username: vinniefalco  
> Created_at: 2022-08-03 22:18:45 UTC  
> Url: https://github.com/boostorg/url/pull/307#discussion_r937183130  

if we get to pick the macro name why not `BOOST_URL_POS` for now

> Username: alandefreitas  
> Created_at: 2022-08-03 23:38:40 UTC  
> Url: https://github.com/boostorg/url/pull/307#discussion_r937220023  

I like `BOOST_URL_CURRENT_LOCATION` because then I don't have to remember two things. But both are fine.


---

## Review 2 [Commented]

> Username: alandefreitas  
> Created_at: 2022-08-03 23:43:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/307#pullrequestreview-1061119665  

📁 example/magnet/magnet.cpp

```diff
  10 | #include <boost/url/rfc/absolute_uri_rule.hpp>
  11 | #include <boost/url/grammar/digit_chars.hpp>
  12 |+ #include <boost/url/grammar/parse.hpp>
```

> Username: alandefreitas  
> Created_at: 2022-08-03 23:43:20 UTC  
> Url: https://github.com/boostorg/url/pull/307#discussion_r937221934  

I included this in a separate commit in passing to avoid the CI errors.   
  
This is already fixed in passing in another PR, but this one is a simpler and a higher priority one.


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-08-03 23:56:38 UTC  
> Updated_at: 2022-08-30 20:49:22 UTC  
> Url: https://github.com/boostorg/url/pull/307#issuecomment-1204594367  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/307?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#307](https://codecov.io/gh/CPPAlliance/url/pull/307?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (954eb5b) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/954eb5b7e6af8228fa3b8a644c6a2e51b29980e7?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (954eb5b) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 954eb5b differs from pull request most recent head 6e2724b. Consider uploading reports for the commit 6e2724b to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/307/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/307?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #307   +/-   ##  
========================================  
  Coverage    97.92%   97.92%             
========================================  
  Files          132      132             
  Lines         6349     6349             
========================================  
  Hits          6217     6217             
  Misses         132      132             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/307?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/307?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [954eb5b...6e2724b](https://codecov.io/gh/CPPAlliance/url/pull/307?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
  
</details>

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-08-05 16:54:48 UTC  
> Url: https://github.com/boostorg/url/pull/307#issuecomment-1206657858  

Rebased

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-09 00:04:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/307#pullrequestreview-1065927852  

📁 include/boost/url/impl/authority_view.ipp

```diff
 258 |             "authority_view::max_size exceeded",
 259 |-             BOOST_CURRENT_LOCATION);
 259 |+             BOOST_URL_POS);
```

> Username: vinniefalco  
> Created_at: 2022-08-09 00:04:32 UTC  
> Url: https://github.com/boostorg/url/pull/307#discussion_r940763712  

If Peter does it this way, why can't we? https://github.com/boostorg/system/blob/cc7c2f7ee42623aef41c8cb45fe8d13f7bc87d7f/include/boost/system/result.hpp#L236  
  
He puts the macro in the parameter list as a default argument... can we do that in `throw_length_error`?


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-24 19:07:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/307#pullrequestreview-1084374878  

📁 include/boost/url/impl/url_base.ipp

```diff
1728 |-         s, detail::fragment_rule).value(
1729 |-             BOOST_CURRENT_LOCATION);
1728 |+         s, detail::fragment_rule).value(BOOST_URL_POS);
```

> Username: vinniefalco  
> Created_at: 2022-08-24 19:07:38 UTC  
> Updated_at: 2022-08-24 19:07:39 UTC  
> Url: https://github.com/boostorg/url/pull/307#discussion_r954196330  

do we even need to pass something to `value`? It already has a default parameter with the source location.


---
