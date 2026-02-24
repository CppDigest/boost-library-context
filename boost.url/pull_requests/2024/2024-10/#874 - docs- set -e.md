# #874 docs: set -e [Merged]

> Username: sdarwin  
> Created at: 2024-10-28 13:43:01 UTC  
> Updated at: 2024-10-28 15:50:21 UTC  
> Merged at: 2024-10-28 15:50:02 UTC  
> Closed at: 2024-10-28 15:50:02 UTC  
> Url: https://github.com/boostorg/url/pull/874  

Running `build_antora.sh` in CI jobs there is no way of ascertaining if it failed since the last command is  
  
`echo "Done"`  
  
which always returns 0.    A solution could be to move `npx antora` as the last command, so its exit code will be the same as the script itself.   Or enable the bash setting "Exit immediately if a command exits with a non-zero status."

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-10-28 13:48:36 UTC  
> Url: https://github.com/boostorg/url/pull/874#issuecomment-2441643038  

Antora version: an automated preview of the documentation is available at [https://874.urlantora.prtest2.cppalliance.org/site/index.html](https://874.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-10-28 14:10:27 UTC  
> Url: https://github.com/boostorg/url/pull/874#issuecomment-2441702342  

GCOVR code coverage report [https://874.url.prtest2.cppalliance.org/gcovr/index.html](https://874.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://874.url.prtest2.cppalliance.org/genhtml/index.html](https://874.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://874.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://874.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-10-28 14:12:14 UTC  
> Updated_at: 2024-10-28 15:50:21 UTC  
> Url: https://github.com/boostorg/url/pull/874#issuecomment-2441707112  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/874?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.20%. Comparing base [(`d8cacb5`)](https://app.codecov.io/gh/boostorg/url/commit/d8cacb56584170a49923fbbe60dfa83014e1b4d3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`d136947`)](https://app.codecov.io/gh/boostorg/url/commit/d136947146ecba29299e1994defe77a20c9822d1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/874/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/874?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #874   +/-   ##  
========================================  
  Coverage    99.20%   99.20%             
========================================  
  Files          157      157             
  Lines         8415     8415             
========================================  
  Hits          8348     8348             
  Misses          67       67             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/874?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/874?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d8cacb5...d136947](https://app.codecov.io/gh/boostorg/url/pull/874?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
