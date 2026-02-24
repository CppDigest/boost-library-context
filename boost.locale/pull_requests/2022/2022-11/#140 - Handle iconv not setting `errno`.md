# #140 Handle iconv not setting `errno` [Merged]

> Username: Flamefire  
> Created at: 2022-11-24 14:43:52 UTC  
> Updated at: 2022-11-25 08:47:00 UTC  
> Merged at: 2022-11-25 08:44:27 UTC  
> Closed at: 2022-11-25 08:44:27 UTC  
> Url: https://github.com/boostorg/locale/pull/140  

Some iconv implementations seem to not set `errno`.  
So check if it could be due to insufficient space else assume an encoding error and skip the current char.  
  
Observed when using the StrawberryPerl libiconv which is built using the MSYS2-MinGW GCC 8.3 which seemingly doesn't work well with MSVC (i.e. errno is set in the library but not visible in MSVC compiled libraries)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-24 19:50:23 UTC  
> Url: https://github.com/boostorg/locale/pull/140#issuecomment-1326801235  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/140?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#140](https://codecov.io/gh/boostorg/locale/pull/140?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d39e4a8) into [develop](https://codecov.io/gh/boostorg/locale/commit/1cc2a017ab5c990390ec89315cef71dd6b3a8471?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1cc2a01) will **increase** coverage by `0.04%`.  
> The diff coverage is `97.29%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/140/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/140?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #140      +/-   ##  
===========================================  
+ Coverage    88.00%   88.04%   +0.04%       
===========================================  
  Files          104      104                
  Lines         9154     9160       +6       
===========================================  
+ Hits          8056     8065       +9       
+ Misses        1098     1095       -3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/140?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/iconv\_codepage.ipp](https://codecov.io/gh/boostorg/locale/pull/140/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9pY29udl9jb2RlcGFnZS5pcHA=) | `95.23% <92.30%> (+4.32%)` | :arrow_up: |  
| [test/test\_codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/140/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlLmNwcA==) | `95.01% <100.00%> (+0.15%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/140?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/140?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1cc2a01...d39e4a8](https://codecov.io/gh/boostorg/locale/pull/140?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
