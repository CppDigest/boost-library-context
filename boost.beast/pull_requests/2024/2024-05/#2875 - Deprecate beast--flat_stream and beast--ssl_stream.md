# #2875 Deprecate beast::flat_stream and beast::ssl_stream [Merged]

> Username: ashtum  
> Created at: 2024-05-30 12:11:42 UTC  
> Updated at: 2024-05-31 13:15:53 UTC  
> Merged at: 2024-05-31 13:15:52 UTC  
> Closed at: 2024-05-31 13:15:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2875  

Closes #1995

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-05-30 12:23:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2875#issuecomment-2139438908  

An automated preview of the documentation is available at [https://2875.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2875.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-05-30 13:20:09 UTC  
> Updated_at: 2024-05-31 09:18:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2875#issuecomment-2139543108  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2875?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.21%. Comparing base [(`cf72589`)](https://app.codecov.io/gh/boostorg/beast/commit/cf72589ae9a5fb2a4ae0bef620ad8ee6b3926770?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a0d2fe4`)](https://app.codecov.io/gh/boostorg/beast/commit/a0d2fe49752f899c417f9c9c7aa413578a58cca3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2875/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2875?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2875      +/-   ##  
===========================================  
+ Coverage    93.05%   93.21%   +0.16%       
===========================================  
  Files          178      177       -1       
  Lines        13694    13670      -24       
===========================================  
  Hits         12743    12743                
+ Misses         951      927      -24       
```  
  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2875/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2875?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2875?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cf72589...a0d2fe4](https://app.codecov.io/gh/boostorg/beast/pull/2875?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2024-05-30 20:51:30 UTC  
> Updated_at: 2024-05-30 20:51:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2875#issuecomment-2140845472  

Commit messages should start with nouns:  
  
std::bind is superfluous in some examples  
ssl_stream does not use flat_stream  
net::ssl::stream is canonical in examples  
net::ssl::stream is canonical in snippets  
ssl_stream and flat_stream are deprecated

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-05-31 09:33:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2875#issuecomment-2141608571  

An automated preview of the documentation is available at [https://2875.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2875.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-05-31 13:13:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2875#issuecomment-2142131441  

An automated preview of the documentation is available at [https://2875.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2875.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---
