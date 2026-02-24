# #1073 missing BOOST_FALLTHROUGH in a few cases [Merged]

> Username: RoyBellingan  
> Created at: 2025-02-15 15:03:28 UTC  
> Updated at: 2025-02-26 11:08:16 UTC  
> Merged at: 2025-02-24 05:41:09 UTC  
> Closed at: 2025-02-24 05:41:09 UTC  
> Url: https://github.com/boostorg/json/pull/1073  

After seeing a few test failing for #1071 I added a few more check in my local build, and those 3 popped out

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-02-15 15:13:19 UTC  
> Url: https://github.com/boostorg/json/pull/1073#issuecomment-2660965912  

An automated preview of the documentation is available at [https://1073.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1073.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-02-15 15:51:06 UTC  
> Updated_at: 2025-02-26 11:08:16 UTC  
> Url: https://github.com/boostorg/json/pull/1073#issuecomment-2660979344  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1073?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.70%. Comparing base [(`c02d872`)](https://app.codecov.io/gh/boostorg/json/commit/c02d8721efeb3c4f7b9d86758a48e11edb523ae5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e3531d3`)](https://app.codecov.io/gh/boostorg/json/commit/e3531d3210ef02705d88ef241cb9c6e2a4977602?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1073/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1073?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1073   +/-   ##  
========================================  
  Coverage    93.70%   93.70%             
========================================  
  Files           91       91             
  Lines         9139     9139             
========================================  
  Hits          8564     8564             
  Misses         575      575             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1073?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/1073?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fbasic_parser_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.50% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1073?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1073?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c02d872...e3531d3](https://app.codecov.io/gh/boostorg/json/pull/1073?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-02-15 16:54:18 UTC  
> Url: https://github.com/boostorg/json/pull/1073#issuecomment-2661002367  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1073/pullrequest-condensed-9d90eeb.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1073/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1073/pullrequest.html)

---

## Review 4 [Changes requested]

> Username: grisumbras  
> Created_at: 2025-02-19 06:03:07 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/json/pull/1073#pullrequestreview-2625707596  

📁 include/boost/json/basic_parser_impl.hpp

```diff
 690 |+ 
 691 |         case ' ':
 692 |+             BOOST_FALLTHROUGH;
```

> Username: grisumbras  
> Created_at: 2025-02-19 06:02:50 UTC  
> Updated_at: 2025-02-19 06:03:07 UTC  
> Url: https://github.com/boostorg/json/pull/1073#discussion_r1961001204  

This and the next few are not necessary. It's a common idiom to group cases like this. I don't believe any compiler would complain.

> Username: RoyBellingan  
> Created_at: 2025-02-19 13:14:20 UTC  
> Updated_at: 2025-02-19 13:15:48 UTC  
> Url: https://github.com/boostorg/json/pull/1073#discussion_r1961660127  

So, -Wall -Wextra -Wpedantic do not trigger this warning, you need to explicitly have  
 -Wimplicit-fallthrough  
  
So in my case clang (since version 13 ) gives the warning, but gcc14 not   
  
```bash  
clang++  -I/home/roy/Public3/boostIntrusive/boostjson/include -fdiagnostics-color=always -Wimplicit-fallthrough -c /home/roy/Public3/boostIntrusive/src.cpp  
  
In file included from /home/roy/Public3/boostIntrusive/src.cpp:1:  
In file included from /home/roy/Public3/boostIntrusive/boostjson/include/boost/json/src.hpp:27:  
In file included from /home/roy/Public3/boostIntrusive/boostjson/include/boost/json.hpp:26:  
In file included from /home/roy/Public3/boostIntrusive/boostjson/include/boost/json/parse_into.hpp:216:  
In file included from /home/roy/Public3/boostIntrusive/boostjson/include/boost/json/impl/parse_into.hpp:14:  
/home/roy/Public3/boostIntrusive/boostjson/include/boost/json/basic_parser_impl.hpp:690:9: error: unannotated fall-through between switch labels [-Werror,-Wimplicit-fallthrough]  
  690 |         case ' ':  
      |         ^  
 ```

> Username: grisumbras  
> Created_at: 2025-02-19 16:39:48 UTC  
> Url: https://github.com/boostorg/json/pull/1073#discussion_r1962022507  

GCC doesn't seem to issue a warning there even with the explicit flag. To be honest, marking every empty case like this looks very ugly, so I'm going to bite the bullet and insist you remove those lines.


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-02-19 18:19:57 UTC  
> Url: https://github.com/boostorg/json/pull/1073#issuecomment-2669428712  

An automated preview of the documentation is available at [https://1073.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1073.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-02-19 20:28:45 UTC  
> Url: https://github.com/boostorg/json/pull/1073#issuecomment-2669689773  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1073/pullrequest-condensed-48bd79b.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1073/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1073/pullrequest.html)

---

## Comment 7

> Username: grisumbras  
> Created_at: 2025-02-24 05:41:27 UTC  
> Url: https://github.com/boostorg/json/pull/1073#issuecomment-2677481405  

Thank you for your contribution.

---

## Comment 8

> Username: RoyBellingan  
> Created_at: 2025-02-24 06:57:09 UTC  
> Url: https://github.com/boostorg/json/pull/1073#issuecomment-2677570873  

Each Journey begin with one step!

---
