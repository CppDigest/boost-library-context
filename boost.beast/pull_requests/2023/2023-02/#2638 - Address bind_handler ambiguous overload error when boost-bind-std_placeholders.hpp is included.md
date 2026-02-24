# #2638 Address bind_handler ambiguous overload error when boost/bind/std_placeholders.hpp is included [Merged]

> Username: ednolan  
> Created at: 2023-02-15 21:37:33 UTC  
> Updated at: 2023-02-17 05:50:05 UTC  
> Merged at: 2023-02-17 05:50:05 UTC  
> Closed at: 2023-02-17 05:50:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2638  

Previously, the implementation of `bind_handler` made the assumption that the trait `boost::is_placeholder` would be false for types corresponding to values in the `std::placeholders` namespace.  
  
However, boost/bind commit `c85b31e3d200dda2a73cf0027a82c6d8e29066f8`, `Support use of standard placeholders with boost::bind`, added a new header, `boost/bind/std_placeholders.hpp`, which adds specializations to  
 `boost::is_placeholder` for `std::placeholders` types.  
  
When this header is included before a use of `boost::bind_handler`, it results in compiler errors like the following, due to an internal helper function having overloads for  
`enable_if<boost::is_placeholder...>` and  
`enable_if<std::is_placeholder...>`, which were previously assumed to be mutually exclusive, but for which both conditions now are true:  
  
```  
../../../boost/beast/core/detail/bind_handler.hpp:126:18: error: call of overloaded 'extract(std::_Placeholder<1>, boost::beast::detail::tuple<int&&>)' is ambiguous  
  126 |         h(extract(detail::get<S>(std::move(args)),  
      |           ~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  127 |             std::forward<ValsTuple>(vals))...);  
      |             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
This header is included in `boost/bind/bind.hpp`, and is transitively included by headers in many boost libraries including:  
  
algorithm  
asio  
atomic  
graph  
msm  
multi_index  
property_tree  
ptr_container  
python  
signals2  
test  
thread  
  
Making it possible that an include from one of these libraries will randomly cause `bind_handler` to fail to compile.  
  
This change addresses the issue by ensuring that `boost/bind/std_placeholders.hpp` is included in the bind_handler implementation file, and adjusting the SFINAE for the boost::is_placeholder overload so that it triggers only  
if std::is_placeholder is false. This approach is necessary over simply removing the std::is_placeholder overload because std_placeholders.hpp might have had its functionality suppressed by feature guards. This change also explicitly adds a `boost/bind/std_placeholders.hpp` include to the `bind_handler` unit test file to prevent this issue from regressing.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2023-02-15 21:41:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2638#issuecomment-1432074485  

Thank you for your contribution!

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-02-15 22:29:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2638#issuecomment-1432149555  

![pullrequest](https://2638.beast.tracing.cppalliance.org/pullrequest-3c1ca6fc.png)  
Timeline tracing charts: [https://2638.beast.tracing.cppalliance.org/index.html](https://2638.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-02-15 23:01:06 UTC  
> Updated_at: 2023-02-15 23:25:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2638#issuecomment-1432194660  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2638?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2638](https://codecov.io/gh/boostorg/beast/pull/2638?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (98b681b) into [develop](https://codecov.io/gh/boostorg/beast/commit/341ac7591b2b023c81de13312a80d1e824742a1c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (341ac75) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2638/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2638?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2638   +/-   ##  
========================================  
  Coverage    92.93%   92.93%             
========================================  
  Files          177      177             
  Lines        13651    13651             
========================================  
  Hits         12687    12687             
  Misses         964      964             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2638?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/2638?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2638?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2638?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [341ac75...98b681b](https://codecov.io/gh/boostorg/beast/pull/2638?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-02-16 02:53:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2638#issuecomment-1432411664  

Looks good; but can't we just include the `std_placeholders.hpp` and just add one function? Or do you want to keep the headers to a minumum @vinniefalco ?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2023-02-16 02:57:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2638#issuecomment-1432413793  

That's up to you. Don't break anything.

---

## Comment 6

> Username: ednolan  
> Created_at: 2023-02-16 02:58:42 UTC  
> Updated_at: 2023-02-16 02:58:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2638#issuecomment-1432416006  

> can't we just include the std_placeholders.hpp and just add one function  
  
If what you mean by this is, can't we have a single overload handling the std::placeholders and boost::placeholders cases: that was my original approach. But the problem is that `std_placeholders.hpp` isn't guaranteed to actually work for `std::placeholders` because it can be turned off by feature gating. This caused my original commit to fail CI for GCC 4. So you still need two overloads.

---
