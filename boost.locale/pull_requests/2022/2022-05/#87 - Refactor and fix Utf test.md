# #87 Refactor and fix Utf test [Merged]

> Username: Flamefire  
> Created at: 2022-05-31 09:09:25 UTC  
> Updated at: 2022-06-01 20:20:30 UTC  
> Merged at: 2022-06-01 20:20:27 UTC  
> Closed at: 2022-06-01 20:20:27 UTC  
> Url: https://github.com/boostorg/locale/pull/87  

- Add tests using char16_t/char32_t when available  
- Rename test_from/test_to to make C&P issue obvious and fix that.  
- Introduce functions for the tests to better group the subtests  
  
Contains the bugfix from #48 and hence closes #48

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-31 10:15:32 UTC  
> Updated_at: 2022-06-01 12:52:40 UTC  
> Url: https://github.com/boostorg/locale/pull/87#issuecomment-1141944402  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/87?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#87](https://codecov.io/gh/boostorg/locale/pull/87?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c79faed) into [develop](https://codecov.io/gh/boostorg/locale/commit/85f7316296afe205a851e26c42a0d0c476d3fe54?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (85f7316) will **increase** coverage by `0.06%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #87      +/-   ##  
===========================================  
+ Coverage    79.81%   79.88%   +0.06%       
===========================================  
  Files           76       76                
  Lines         5911     5931      +20       
===========================================  
+ Hits          4718     4738      +20       
  Misses        1193     1193                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/87?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_utf.cpp](https://codecov.io/gh/boostorg/locale/pull/87/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V0Zi5jcHA=) | `98.31% <100.00%> (+0.13%)` | :arrow_up: |  
| [include/boost/locale/utf.hpp](https://codecov.io/gh/boostorg/locale/pull/87/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRmLmhwcA==) | `98.22% <0.00%> (+0.07%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/87?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/87?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [85f7316...c79faed](https://codecov.io/gh/boostorg/locale/pull/87?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
