# #158 Fix clang 10 C++17 warnings about deprecated implicit assignment operators [Merged]

> Username: Lastique  
> Created at: 2020-06-13 23:55:38 UTC  
> Updated at: 2020-06-23 20:40:12 UTC  
> Merged at: 2020-06-23 20:28:17 UTC  
> Closed at: 2020-06-23 20:28:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/158  

C++17 deprecates implicit generation of assignment operator in presence of explicitly declared copy constructor. In future standards this behavior may be removed.  
  
In the affected Boost.DateTime classes, the explicitly defined copy constructors are functionally equivalent to those would be generated implicitly, so we can just remove them. The additional benefit is that the implicitly generated ones will have `constexpr` and `noexcept` specifications, and will also allow implicit move constructors and assignment operators.  
  
The warnings can be reproduced with this command line:  
  
```  
b2 -j8 toolset=clang cxxstd=17 warnings=extra  
```  
  
For example:  
  
```  
../../../boost/date_time/date_duration.hpp:47:27: warning: definition of implicit copy assignment operator for 'date_duration<boost::date_time::duration_traits_adapted>' is deprecated because it has a user-declared copy constructor [-Wdeprecated-copy]  
    BOOST_CXX14_CONSTEXPR date_duration(const date_duration<duration_rep_traits>& other) :  
                          ^  
../../../boost/date_time/gregorian/greg_duration.hpp:26:30: note: in implicit copy assignment operator for 'boost::date_time::date_duration<boost::date_time::duration_traits_adapted>' first required here  
  class BOOST_SYMBOL_VISIBLE date_duration :  
                             ^  
gregorian/testdate_duration.cpp:44:10: note: in implicit copy assignment operator for 'boost::gregorian::date_duration' first required here  
  oneDay = twoDays / 2;  
         ^  
```  
  
After the fix, a few similar warnings from Boost.Serialization may be left.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-06-14 02:13:22 UTC  
> Updated_at: 2020-06-14 02:14:42 UTC  
> Url: https://github.com/boostorg/date_time/pull/158#issuecomment-643707695  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/158?src=pr&el=h1) Report  
> Merging [#158](https://codecov.io/gh/boostorg/date_time/pull/158?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/356c5c26008a7c09aaeb75d83eb62fd93684f109&el=desc) will **increase** coverage by `0.07%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/158/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g)](https://codecov.io/gh/boostorg/date_time/pull/158?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #158      +/-   ##  
===========================================  
+ Coverage    52.71%   52.79%   +0.07%       
===========================================  
  Files           76       76                
  Lines         4490     4480      -10       
  Branches      2243     2236       -7       
===========================================  
- Hits          2367     2365       -2       
  Misses         391      391                
+ Partials      1732     1724       -8       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/158?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/date\_duration.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZGF0ZV9kdXJhdGlvbi5ocHA=) | `96.42% <ø> (-0.35%)` | :arrow_down: |  
| [...nclude/boost/date\_time/gregorian/greg\_duration.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfZHVyYXRpb24uaHBw) | `90.62% <ø> (-0.56%)` | :arrow_down: |  
| [include/boost/date\_time/period\_parser.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcGVyaW9kX3BhcnNlci5ocHA=) | `39.39% <ø> (-2.28%)` | :arrow_down: |  
| [include/boost/date\_time/special\_values\_parser.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvc3BlY2lhbF92YWx1ZXNfcGFyc2VyLmhwcA==) | `54.16% <ø> (+0.32%)` | :arrow_up: |  
| [include/boost/date\_time/time\_duration.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9kdXJhdGlvbi5ocHA=) | `81.25% <ø> (-0.57%)` | :arrow_down: |  
| [include/boost/date\_time/tz\_db\_base.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdHpfZGJfYmFzZS5ocHA=) | `40.20% <0.00%> (-2.66%)` | :arrow_down: |  
| [include/boost/date\_time/c\_local\_time\_adjustor.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvY19sb2NhbF90aW1lX2FkanVzdG9yLmhwcA==) | `10.00% <0.00%> (-0.53%)` | :arrow_down: |  
| [include/boost/date\_time/time\_facet.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9mYWNldC5ocHA=) | `35.58% <0.00%> (-0.02%)` | :arrow_down: |  
| [...ude/boost/date\_time/local\_time/local\_date\_time.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvbG9jYWxfdGltZS9sb2NhbF9kYXRlX3RpbWUuaHBw) | `48.16% <0.00%> (+1.04%)` | :arrow_up: |  
| [include/boost/date\_time/time\_system\_split.hpp](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9zeXN0ZW1fc3BsaXQuaHBw) | `27.41% <0.00%> (+1.61%)` | :arrow_up: |  
| ... and [3 more](https://codecov.io/gh/boostorg/date_time/pull/158/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/158?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/158?src=pr&el=footer). Last update [356c5c2...8251acc](https://codecov.io/gh/boostorg/date_time/pull/158?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-06-23 19:24:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/158#issuecomment-648369936  

Ping? It would be nice to have this in 1.74.

---

## Review 3 [Commented]

> Username: JeffGarland  
> Created_at: 2020-06-23 20:00:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/158#pullrequestreview-436110890  

📁 include/boost/date_time/gregorian/greg_duration.hpp

```diff
  42 |-     //! Copy constructor
  43 |-     BOOST_CXX14_CONSTEXPR
  44 |-     date_duration(const date_duration& other) : base_type(static_cast< base_type const& >(other))
```

> Username: JeffGarland  
> Created_at: 2020-06-23 19:59:46 UTC  
> Updated_at: 2020-06-23 20:00:21 UTC  
> Url: https://github.com/boostorg/date_time/pull/158#discussion_r444473041  

so we're depending on the compiler to generate this now, is that the strategy?

> Username: Lastique  
> Created_at: 2020-06-23 20:05:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/158#discussion_r444475837  

Yes.

> Username: JeffGarland  
> Created_at: 2020-06-23 20:27:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/158#discussion_r444487115  

Right, so are we sure that works on all the compilers including back to 98?  I'm not confident that the tests cover it all.  As much as I hate it I'm wondering if we should #ifdef -- hmm well maybe we just push it and see how the regressions go.  I'll do that.  And sorry, I lost track of the calendar -- didn't realize 74 was closing so soon.


---

## Comment 4

> Username: JeffGarland  
> Created_at: 2020-06-23 20:34:41 UTC  
> Url: https://github.com/boostorg/date_time/pull/158#issuecomment-648402942  

I suspect this will also resolve issue #139 -- will watch regressions.

---
