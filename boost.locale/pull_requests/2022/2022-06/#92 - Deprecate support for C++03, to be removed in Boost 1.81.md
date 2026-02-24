# #92 Deprecate support for C++03, to be removed in Boost 1.81 [Merged]

> Username: Flamefire  
> Created at: 2022-06-05 09:25:36 UTC  
> Updated at: 2022-06-13 09:08:29 UTC  
> Merged at: 2022-06-13 09:08:27 UTC  
> Closed at: 2022-06-13 09:08:27 UTC  
> Url: https://github.com/boostorg/locale/pull/92  

Make users aware of the pending removal for pre-C++11 support of Boost.Locale in Boost 1.81.  
  
If there are concerns and/or strong interest in keeping C++03 support users should comment on #90

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-06-05 09:56:25 UTC  
> Url: https://github.com/boostorg/locale/pull/92#issuecomment-1146775737  

- `BOOST_NO_CXX11_HDR_TYPE_TRAITS` excludes GCC < 5.1 (released 2015)  
- `BOOST_NO_CXX11_NOEXCEPT` excludes MSVC < 2015  
  
I find that reasonable requirements for a library that will be released in mid/end 2022.  
  
@artyom-beilis Objections?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-06-05 13:11:41 UTC  
> Url: https://github.com/boostorg/locale/pull/92#issuecomment-1146802283  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/92?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#92](https://codecov.io/gh/boostorg/locale/pull/92?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (61a56cf) into [develop](https://codecov.io/gh/boostorg/locale/commit/5b3aae0f8efc103d03e04066613bec9b68e71861?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5b3aae0) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #92   +/-   ##  
========================================  
  Coverage    79.92%   79.92%             
========================================  
  Files           76       76             
  Lines         5944     5944             
========================================  
  Hits          4751     4751             
  Misses        1193     1193             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/92?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/92?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5b3aae0...61a56cf](https://codecov.io/gh/boostorg/locale/pull/92?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
