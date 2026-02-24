# #150 Factor out iconv_codecvt and remove use of `std::shared_ptr` [Merged]

> Username: Flamefire  
> Created at: 2023-03-05 15:16:06 UTC  
> Updated at: 2023-03-06 15:37:42 UTC  
> Merged at: 2023-03-06 15:37:38 UTC  
> Closed at: 2023-03-06 15:37:38 UTC  
> Url: https://github.com/boostorg/locale/pull/150  

Iconv is also available on Windows so not specific to the POSIX backend.  
  
Instead of a shared_ptr to a vector we can simply use a `std::array`.  
  
Finally the `iconv_t` can be put into an RAII class.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-06 13:57:58 UTC  
> Url: https://github.com/boostorg/locale/pull/150#issuecomment-1456184539  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#150](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (06d865b) into [develop](https://codecov.io/gh/boostorg/locale/commit/6cf1f959e5e74c4b41738e14aa8e1180ffb93260?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6cf1f95) will **increase** coverage by `0.04%`.  
> The diff coverage is `91.75%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/150/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #150      +/-   ##  
===========================================  
+ Coverage    89.38%   89.42%   +0.04%       
===========================================  
  Files          108      109       +1       
  Lines         9446     9437       -9       
===========================================  
- Hits          8443     8439       -4       
+ Misses        1003      998       -5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/posix/codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9jb2RlY3Z0LmNwcA==) | `100.00% <ø> (+11.81%)` | :arrow_up: |  
| [src/boost/locale/shared/iconv\_codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvaWNvbnZfY29kZWN2dC5jcHA=) | `90.24% <90.24%> (ø)` | |  
| [src/boost/locale/util/iconv.hpp](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2ljb252LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_codepage\_converter.cpp](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlX2NvbnZlcnRlci5jcHA=) | `99.39% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6cf1f95...06d865b](https://codecov.io/gh/boostorg/locale/pull/150?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
