# #148 Use the last object key [Closed]

> Username: sdkrystian  
> Created at: 2020-08-11 22:18:46 UTC  
> Updated at: 2020-08-22 17:59:38 UTC  
> Closed at: 2020-08-12 17:11:33 UTC  
> Url: https://github.com/boostorg/json/pull/148  

Fixes #71

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 22:24:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/148#pullrequestreview-465469874  

📁 include/boost/json/detail/impl/object_impl.ipp

```diff
  73 |-     auto end = this->end();
  74 |-     for(auto p = begin(); p != end;)
  73 |+     const auto first = begin() - 1;
```

> Username: vinniefalco  
> Created_at: 2020-08-11 22:24:31 UTC  
> Updated_at: 2020-08-12 03:01:38 UTC  
> Url: https://github.com/boostorg/json/pull/148#discussion_r468898468  

Do we need to `BOOST_ASSERT` that the list is not empty?

> Username: sdkrystian  
> Created_at: 2020-08-11 22:51:21 UTC  
> Updated_at: 2020-08-12 03:01:38 UTC  
> Url: https://github.com/boostorg/json/pull/148#discussion_r468908138  

No, `last > first` is `false` if that happens.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-08-11 22:46:24 UTC  
> Url: https://github.com/boostorg/json/pull/148#issuecomment-672334263  

Approved

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-12 00:48:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/148#pullrequestreview-465520454  

📁 include/boost/json/detail/impl/object_impl.ipp

```diff
  75 |+     const auto first = begin() - 1;
  76 |+     auto last = end() - 1;
  77 |+     for(; last > first; --last)
```

> Username: vinniefalco  
> Created_at: 2020-08-12 00:48:53 UTC  
> Updated_at: 2020-08-12 03:01:38 UTC  
> Url: https://github.com/boostorg/json/pull/148#discussion_r468942908  

Can we avoid the extra conditional with this  
```  
auto const first = begin();  
auto last = end();  
while( last-- > first )  
{  
    ...  
```  
?

> Username: vinniefalco  
> Created_at: 2020-08-12 02:19:35 UTC  
> Updated_at: 2020-08-12 03:01:38 UTC  
> Url: https://github.com/boostorg/json/pull/148#discussion_r468966591  

Okay that was no good, this should work though:  
```  
// must work when table pointer is null  
auto const first = begin();  
for( auto last = end(); last > first; )  
{  
    --last;  
    ...  
```

> Username: sdkrystian  
> Created_at: 2020-08-12 02:53:19 UTC  
> Updated_at: 2020-08-12 03:01:38 UTC  
> Url: https://github.com/boostorg/json/pull/148#discussion_r468975131  

Did you change this on your branch or should I change it here?

> Username: vinniefalco  
> Created_at: 2020-08-12 02:57:06 UTC  
> Updated_at: 2020-08-12 03:01:38 UTC  
> Url: https://github.com/boostorg/json/pull/148#discussion_r468976094  

I prefer not to make these kinds of changes on my own, because then it can be confusing if you are also making the change, but I already merged it, and maybe you found a bug so now you have a branch that is different than mine. Since you're the lead on this pull request, it is better if you are the only one applying changes to it.

> Username: sdkrystian  
> Created_at: 2020-08-12 02:58:19 UTC  
> Updated_at: 2020-08-12 03:01:38 UTC  
> Url: https://github.com/boostorg/json/pull/148#discussion_r468976377  

Copy that


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2020-08-12 14:54:43 UTC  
> Updated_at: 2020-08-12 16:44:54 UTC  
> Url: https://github.com/boostorg/json/pull/148#issuecomment-672921964  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/148?src=pr&el=h1) Report  
> Merging [#148](https://codecov.io/gh/CPPAlliance/json/pull/148?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/ec29dfa768a16672534f758005dfe4841a8ea93d&el=desc) will **increase** coverage by `0.06%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/148/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/148?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #148      +/-   ##  
===========================================  
+ Coverage    98.34%   98.41%   +0.06%       
===========================================  
  Files           63       63                
  Lines         5754     5751       -3       
===========================================  
+ Hits          5659     5660       +1       
+ Misses          95       91       -4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/148?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/object\_impl.ipp](https://codecov.io/gh/CPPAlliance/json/pull/148/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL29iamVjdF9pbXBsLmlwcA==) | `98.50% <100.00%> (+5.65%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/148?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/148?src=pr&el=footer). Last update [ec29dfa...5a7a2be](https://codecov.io/gh/CPPAlliance/json/pull/148?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
