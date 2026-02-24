# #154 Break DateTime <-> Serialization circular dependency [Merged]

> Username: Kojoley  
> Created at: 2020-05-15 16:47:16 UTC  
> Updated at: 2020-05-25 18:33:39 UTC  
> Merged at: 2020-05-25 16:39:22 UTC  
> Closed at: 2020-05-25 16:39:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/154  

Breaks DateTime -> Serialization -> Spirit -> Thread -> DateTime chain.  
  
The Serialization documentation seems to be explicitly allow this:  
https://www.boost.org/doc/libs/1_72_0/libs/serialization/doc/traits.html#version  
https://www.boost.org/doc/libs/1_73_0/libs/serialization/doc/serialization.html#splitting

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-16 02:44:25 UTC  
> Updated_at: 2020-05-23 19:15:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#issuecomment-629577113  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/154?src=pr&el=h1) Report  
> Merging [#154](https://codecov.io/gh/boostorg/date_time/pull/154?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/3d55c15cbbff085e56b221237c1e6b585a478de9&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/154/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g)](https://codecov.io/gh/boostorg/date_time/pull/154?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #154      +/-   ##  
===========================================  
- Coverage    52.72%   52.71%   -0.02%       
===========================================  
  Files           76       76                
  Lines         4491     4490       -1       
  Branches      2243     2243                
===========================================  
- Hits          2368     2367       -1       
  Misses         391      391                
  Partials      1732     1732                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/154?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/date\_time/gregorian/greg\_serialize.hpp](https://codecov.io/gh/boostorg/date_time/pull/154/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfc2VyaWFsaXplLmhwcA==) | `43.06% <100.00%> (-0.42%)` | :arrow_down: |  
| [...lude/boost/date\_time/posix\_time/time\_serialize.hpp](https://codecov.io/gh/boostorg/date_time/pull/154/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS90aW1lX3NlcmlhbGl6ZS5ocHA=) | `28.98% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/154?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/154?src=pr&el=footer). Last update [3d55c15...d1bfd6a](https://codecov.io/gh/boostorg/date_time/pull/154?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: JeffGarland  
> Created_at: 2020-05-22 16:53:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/154#pullrequestreview-417055531  

📁 include/boost/date_time/posix_time/time_serialize.hpp

```diff
  29 |+ 
  30 |+ template<>
  31 |+ struct version<boost::posix_time::time_duration>
```

> Username: JeffGarland  
> Created_at: 2020-05-22 16:53:38 UTC  
> Updated_at: 2020-05-23 14:20:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#discussion_r429354816  

Is this what serialization requires for this?  Can it not just be a const static?

> Username: Kojoley  
> Created_at: 2020-05-22 17:06:24 UTC  
> Updated_at: 2020-05-23 14:20:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#discussion_r429360395  

Initially I have done exactly that, and it is what documentations says, but in reality Serialization ODR-uses the value and you get linking error, to fix it you need an out of class definition, so it is simpler to inherit from `integral_constant`.

> Username: JeffGarland  
> Created_at: 2020-05-22 17:23:51 UTC  
> Updated_at: 2020-05-23 14:20:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#discussion_r429367860  

right, but it requires another header dependency -- which of course in c++11 should really be std:: instead of boost.  and maybe it should even just be constexpr which I think should avoid the linking issue.

> Username: Kojoley  
> Created_at: 2020-05-22 17:27:08 UTC  
> Updated_at: 2020-05-23 14:20:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#discussion_r429369289  

> right, but it requires another header dependency   
  
The other DateTime parts are implicitly include it already anyway  
  
> and maybe it should even just be constexpr which I think should avoid the linking issue.  
  
AFAIK only `inline` keyword will solve the ODR violation, but it requires C++17.

> Username: JeffGarland  
> Created_at: 2020-05-22 17:44:41 UTC  
> Updated_at: 2020-05-23 14:20:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#discussion_r429376939  

> The other DateTime parts are implicitly include it already anyway  
  
Ok, that's fair.  
  
> AFAIK only inline keyword will solve the ODR violation, but it requires C++17.  
  
yep, that's true.


---

## Comment 3

> Username: JeffGarland  
> Created_at: 2020-05-22 17:03:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#issuecomment-632807688  

So I guess one question about this is how much user code is broken by this changes -- is it none since users already had to include the particular archive type to use serialization?   
  
Thinking about this from a design point of view I like breaking the header dependency, although practically speaking this doesn't extend for the whole library since tests still have serialization dependence - would be nice to conditionally detect lack of serialization and not run those.  The downside here, btw is if serialization ever changes then this code will just break.  But seems unlikely given this interface has been stable for a long time.  
  
**Broader Context:**  
The bigger issue is making date_time less dependent the rest of boost for c++11 and beyond.  lexical_cast is a big one that should be removed in favor of more modern std:: conversion functions.  One wonders if the best way forward is to drop support for 98 to do that.  Also fyi -  on my personal todo list is to add support cereal (a non-boost serialization library).  That code is in production and just needs to be put back into the library.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2020-05-22 17:22:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#issuecomment-632816748  

> So I guess one question about this is how much user code is broken by this changes -- is it none since users already had to include the particular archive type to use serialization?  
  
It should not break any user code because actual serialization routines are included on the user side. I say 'should' because removing some headers always can break some code that unintentionally relies on transient includes, but it is orthogonal problem.  
  
> Thinking about this from a design point of view I like breaking the header dependency, although practically speaking this doesn't extend for the whole library since tests still have serialization dependence - would be nice to conditionally detect lack of serialization and not run those.  
  
My main goal is to have simpler dependency graph for Boost users; as you may expect they are not particularly interested in the test code and its dependencies :-)  
One can write a B2 check to detect missing headers and use it as a condition to turn some tests off, see https://boostorg.github.io/build/manual/develop/index.html#bbv2.reference.rules.check-target-builds  
You probably may be interested in https://github.com/boostorg/optional/issues/66 thread.

---

## Comment 5

> Username: JeffGarland  
> Created_at: 2020-05-22 17:29:09 UTC  
> Updated_at: 2020-05-22 17:29:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#issuecomment-632819589  

> B2 check...  
  
Thanks for the pointer.  
  
> My main goal is to have simpler dependency graph for Boost users; as you may expect they are not particularly interested in the test code and its dependencies :-)  
  
Right, but they might be if you could extract date_time from boost completely -- and yes, that's a big ask given the state of things -- and I'm not sure it's even a goal.  But here's, the thing -- if you're using date_time and serialization together then you already have to have the includes.  If you're not using serialization it already doesn't matter -- this header is not included in the global includes -- it has to be specifically included.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2020-05-22 17:34:41 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#issuecomment-632821950  

> If you're not using serialization it already doesn't matter -- this header is not included in the global includes -- it has to be specifically included.  
  
The thing is that dependency trackers (like https://pdimov.github.io/boostdep-report/develop/date_time.html) and dependency resolver tools (like https://www.boost.org/doc/tools/bcp/) are pessimistic about that and consider it as a hard dependency.

---

## Comment 7

> Username: JeffGarland  
> Created_at: 2020-05-22 18:02:19 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#issuecomment-632832445  

> The thing is that dependency trackers...are pessimistic about that and consider it as a hard dependency.  
  
Well sure.  To be clear, I'm not arguing against doing this -- we will.  And actually now that I'm looking at Peter's report again this is more significant than first glance bc serialization drags in spirit and filesystem -- which is just nonsense.  lexical_cast is maddening bc it drags in 'math' which led to upstream warnings since math is deprecating c++98 (John M did fix it though).

---

## Comment 8

> Username: pdimov  
> Created_at: 2020-05-23 18:32:19 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#issuecomment-633112986  

Once `math` drops C++03 `lexical_cast` will have no excuse to forgo the use of the standard `isinf` functions in favor of `math`'s ones. :-)  
  
And many thanks to Nikita for this PR, these cycles are maddening. Serialization support in a library really shouldn't require Spirit. One is almost driven to rewrite Serialization's XML parser in a PR, with the full knowledge that Robert will then refuse to merge it.

---

## Review 9 [Commented]

> Username: glenfe  
> Created_at: 2020-05-23 18:39:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/154#pullrequestreview-417299606  

📁 include/boost/date_time/posix_time/time_serialize.hpp

```diff
  12 | #include "boost/date_time/posix_time/posix_time.hpp"
  13 | #include "boost/date_time/gregorian/greg_serialize.hpp"
  14 |+ #include "boost/core/nvp.hpp"
```

> Username: glenfe  
> Created_at: 2020-05-23 18:39:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#discussion_r429568699  

<boost/core/nvp.hpp> finally paying dividends.

> Username: Kojoley  
> Created_at: 2020-05-23 18:42:41 UTC  
> Url: https://github.com/boostorg/date_time/pull/154#discussion_r429568905  

Yup, thank you for that!


---
