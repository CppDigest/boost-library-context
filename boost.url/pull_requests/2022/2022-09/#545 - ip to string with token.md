# #545 ip to string with token [Closed]

> Username: alandefreitas  
> Created at: 2022-09-13 23:39:32 UTC  
> Updated at: 2022-09-19 21:29:34 UTC  
> Closed at: 2022-09-15 19:49:17 UTC  
> Url: https://github.com/boostorg/url/pull/545  

fix #434

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-13 23:55:48 UTC  
> Updated_at: 2022-09-14 19:39:29 UTC  
> Url: https://github.com/boostorg/url/pull/545#issuecomment-1246066705  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/545?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@c77e418`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/545/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/545?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff             @@  
##             develop     #545   +/-   ##  
==========================================  
  Coverage           ?   96.71%             
==========================================  
  Files              ?      137             
  Lines              ?     6628             
  Branches           ?        0             
==========================================  
  Hits               ?     6410             
  Misses             ?      218             
  Partials           ?        0             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/545?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/545?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c77e418...e9ecda5](https://codecov.io/gh/boostorg/url/pull/545?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-14 00:07:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/545#pullrequestreview-1106585054  

📁 include/boost/url/impl/ipv6_address.ipp

```diff
 347 |+     std::size_t n2 = print_impl(dest);
 348 |+     BOOST_ASSERT(n == n2);
 349 |+     ignore_unused(n2);
```

> Username: vinniefalco  
> Created_at: 2022-09-14 00:07:25 UTC  
> Url: https://github.com/boostorg/url/pull/545#discussion_r970181094  

did you include ignore_unused.hpp?


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-14 00:07:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/545#pullrequestreview-1106585287  

📁 include/boost/url/impl/ipv6_address.ipp

```diff
 228 |+ std::size_t
 229 |+ ipv6_address::
 230 |+ print_size() const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-14 00:07:53 UTC  
> Url: https://github.com/boostorg/url/pull/545#discussion_r970181228  

bro... this is terrible

> Username: alandefreitas  
> Created_at: 2022-09-14 19:19:44 UTC  
> Url: https://github.com/boostorg/url/pull/545#discussion_r971207767  

Done


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-09-15 17:00:04 UTC  
> Url: https://github.com/boostorg/url/pull/545#issuecomment-1248370826  

merge this when it is ready

---
