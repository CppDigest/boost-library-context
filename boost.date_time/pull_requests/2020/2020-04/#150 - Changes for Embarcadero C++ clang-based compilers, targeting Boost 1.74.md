# #150 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Merged]

> Username: eldiener  
> Created at: 2020-04-18 21:26:43 UTC  
> Updated at: 2020-05-04 16:09:58 UTC  
> Merged at: 2020-05-04 16:09:58 UTC  
> Closed at: 2020-05-04 16:09:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/150  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-04-19 05:17:55 UTC  
> Updated_at: 2020-05-04 11:56:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#issuecomment-616036530  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/150?src=pr&el=h1) Report  
> Merging [#150](https://codecov.io/gh/boostorg/date_time/pull/150?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/ecf042f53800097d47a7c728ab5fa0be00016f53&el=desc) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/150/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g)](https://codecov.io/gh/boostorg/date_time/pull/150?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #150   +/-   ##  
========================================  
  Coverage    52.72%   52.72%             
========================================  
  Files           76       76             
  Lines         4491     4491             
  Branches      2243     2243             
========================================  
  Hits          2368     2368             
  Misses         391      391             
  Partials      1732     1732             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/150?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/date\_duration\_types.hpp](https://codecov.io/gh/boostorg/date_time/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZGF0ZV9kdXJhdGlvbl90eXBlcy5ocHA=) | `80.85% <ø> (ø)` | |  
| [...clude/boost/date\_time/special\_values\_formatter.hpp](https://codecov.io/gh/boostorg/date_time/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvc3BlY2lhbF92YWx1ZXNfZm9ybWF0dGVyLmhwcA==) | `66.66% <ø> (ø)` | |  
| [include/boost/date\_time/string\_parse\_tree.hpp](https://codecov.io/gh/boostorg/date_time/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvc3RyaW5nX3BhcnNlX3RyZWUuaHBw) | `62.71% <ø> (ø)` | |  
| [include/boost/date\_time/time\_system\_split.hpp](https://codecov.io/gh/boostorg/date_time/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9zeXN0ZW1fc3BsaXQuaHBw) | `25.80% <ø> (ø)` | |  
| [...nclude/boost/date\_time/gregorian/greg\_duration.hpp](https://codecov.io/gh/boostorg/date_time/pull/150/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfZHVyYXRpb24uaHBw) | `91.17% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/150?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/150?src=pr&el=footer). Last update [ecf042f...c9e4740](https://codecov.io/gh/boostorg/date_time/pull/150?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: JeffGarland  
> Created_at: 2020-05-03 14:58:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/150#pullrequestreview-404626770  

📁 include/boost/date_time/gregorian/greg_duration.hpp

```diff
  88 |+     #if !defined(BOOST_EMBTC)
  89 |+       
  90 |     BOOST_CXX14_CONSTEXPR friend
```

> Username: JeffGarland  
> Created_at: 2020-05-03 14:55:26 UTC  
> Updated_at: 2020-05-03 21:12:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419114825  

is the issue here that the compiler cannot inline this function for some reason?

> Username: eldiener  
> Created_at: 2020-05-03 15:10:24 UTC  
> Updated_at: 2020-05-03 21:12:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419116797  

It can not find the inlined friend function for exported/imported classes unless the definition is placed outside the class itself. I have reported this to Embarcader with sample code at https://quality.embarcadero.com/browse/RSP-28133.

> Username: JeffGarland  
> Created_at: 2020-05-03 19:53:22 UTC  
> Updated_at: 2020-05-03 21:12:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419151320  

Ok thanks. So maybe the better approach is to not do the macro switch and just do it the same way for all the compilers.  We could leave a comment about borland not supporting to explain why these guys aren't in the class itself.  Anyway, I'd like to minimize the amount of macros switches.  Can you refactor that way?

> Username: JeffGarland  
> Created_at: 2020-05-03 19:54:57 UTC  
> Updated_at: 2020-05-03 21:12:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419151563  

and note you may have to resync the branch from remote because of other updates I resyncd some with develop

> Username: eldiener  
> Created_at: 2020-05-03 20:12:52 UTC  
> Updated_at: 2020-05-03 21:12:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419153698  

Do you mean that you want me to change all similar to what I have done for the Embarcadero C++ clang-based compilers ? I can do that, and resync the PR to include the latest changes from 'develop'.

> Username: JeffGarland  
> Created_at: 2020-05-04 02:41:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419194782  

yes -- the changes for Embarcadero should be valid on all the other compilers too -- might as well just do it one way for all of them without the #ifdef

> Username: eldiener  
> Created_at: 2020-05-04 02:43:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419194946  

I already added the change in my PR.

> Username: JeffGarland  
> Created_at: 2020-05-04 16:09:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419551492  

Thanks -- that looks much cleaner.


📁 include/boost/date_time/locale_config.hpp

```diff
  24 |  || (BOOST_WORKAROUND( BOOST_MSVC, < 1300)) \
  25 |-  || (BOOST_WORKAROUND( __BORLANDC__, BOOST_TESTED_AT( 0x581 )) ) \
  25 |+  || (BOOST_WORKAROUND( BOOST_BORLANDC, BOOST_TESTED_AT( 0x581 )) ) \
```

> Username: JeffGarland  
> Created_at: 2020-05-03 14:57:54 UTC  
> Updated_at: 2020-05-03 21:12:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419115186  

so we're saying this compiler/std library still doesn't have std::locale?  Cause honestly I'd like to remove all of this at this point.   The support for MSVC <1300 for example is long gone in boost.

> Username: eldiener  
> Created_at: 2020-05-03 15:16:17 UTC  
> Updated_at: 2020-05-03 21:12:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419117601  

The BOOST_BORLANDC stands for the old bcc32 compiler, not the current clang-based compiler(s). Feel free to remove all the old code referring to bcc32 if you like since I do not think the old bcc32 can compile current Boost code anyway. I have no interest in trying to get current Boost code to work with the old bcc32 compiler, and I do not test it. I am only trying to get current Boost to work with the much more compliant clang-based compilers.

> Username: JeffGarland  
> Created_at: 2020-05-04 16:08:19 UTC  
> Url: https://github.com/boostorg/date_time/pull/150#discussion_r419550504  

Ok thanks -- I'll probably clean some of this out in 1.74


---
