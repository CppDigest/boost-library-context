# #1664 Add default dtors to satisfy -Wnon-virtual-dtor [Closed]

> Username: mellery451  
> Created at: 2019-07-23 17:09:50 UTC  
> Updated at: 2019-08-29 21:41:59 UTC  
> Closed at: 2019-08-29 21:41:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1664  

I tested with `b2 -j2 libs/beast/test cxxstd=11 cxxflags="-Wnon-virtual-dtor"`, but please advise if other scenarios need to be verified.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-07-23 17:25:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1664#issuecomment-514306006  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1664?src=pr&el=h1) Report  
> Merging [#1664](https://codecov.io/gh/boostorg/beast/pull/1664?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/2ed1c92e036c70aca611315d476f60443d4cc28d?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1664/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1664?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1664      +/-   ##  
===========================================  
- Coverage    92.57%   92.56%   -0.01%       
===========================================  
  Files          156      156                
  Lines        11904    11904                
===========================================  
- Hits         11020    11019       -1       
- Misses         884      885       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1664?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/1664/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.hpp](https://codecov.io/gh/boostorg/beast/pull/1664/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5ocHA=) | `98.22% <0%> (-0.6%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1664?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1664?src=pr&el=footer). Last update [2ed1c92...0cc22f7](https://codecov.io/gh/boostorg/beast/pull/1664?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: djarek  
> Created_at: 2019-07-23 21:14:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1664#pullrequestreview-265681356  

📁 include/boost/beast/core/async_base.hpp

```diff
 250 |     async_base(async_base&& other) = default;
 251 | 
 252 |+     virtual ~async_base() = default;
```

> Username: djarek  
> Created_at: 2019-07-23 21:14:43 UTC  
> Updated_at: 2019-07-23 21:15:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1664#discussion_r306536166  

`virtual` is wrong here, because this class has no `virtual` member functions. Considering the fact that the move constructor is only for exposition in documentation, might as well apply the rule of 0 and wrap the move constructor into an ifdef:  
  
```  
#if BOOST_BEAST_DOXYGEN  
    /// Move Constructor  
    async_base(async_base&& other) = default;  
#endif  
```  
This should silence the warning in regular builds.

> Username: mellery451  
> Created_at: 2019-07-23 23:12:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1664#discussion_r306570971  

isn't `before_invoke_hook` virtual? If I use your suggestion, I still get the warning for `async_base`.

> Username: djarek  
> Created_at: 2019-07-24 15:17:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1664#discussion_r306870119  

Hmm, I missed that. The destructor should be non-virtual(since this class isn't supposed to be used like a typical polymorphic base class) and `protected`. copy/move operations should be `default`ed.

> Username: vinniefalco  
> Created_at: 2019-07-24 21:55:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1664#discussion_r307038596  

Yeah but making the destructor virtual has almost no cost, and satisfies the pedants.

> Username: mellery451  
> Created_at: 2019-07-30 16:57:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1664#discussion_r308833797  

I think I only really need `suite.hpp` changes in order to be able to use the unit_test stuff in our project, so I'd be happy to omit or modify the `async_base` however you see fit. Thanks.

> Username: vinniefalco  
> Created_at: 2019-07-30 17:07:09 UTC  
> Updated_at: 2019-07-30 17:07:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1664#discussion_r308837733  

I think we should keep the destructor virtual. The class already has a virtual member, and making the destructor virtual will silence possible warnings. The cost is so miniscule as to be virtually nil.


---

## Comment 3

> Username: stale[bot]  
> Created_at: 2019-08-29 17:46:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1664#issuecomment-526290815  

This issue has been open for a while with no activity, has it been resolved?

---
