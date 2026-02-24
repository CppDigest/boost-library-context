# #2793 Fix the paragraph separation issue in the documentation [Merged]

> Username: ashtum  
> Created at: 2024-01-10 14:58:46 UTC  
> Updated at: 2024-01-11 14:00:41 UTC  
> Merged at: 2024-01-11 09:41:42 UTC  
> Closed at: 2024-01-11 09:41:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2793  

Fixes #2792

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-01-10 15:08:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2793#issuecomment-1885027854  

An automated preview of the documentation is available at [https://2793.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2793.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: ashtum  
> Created_at: 2024-01-10 15:17:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2793#issuecomment-1885044253  

This breaks some of the new comment blocks because the authors have been relying on the fact that paragraphs in the comment block will not separate in the generated HTML pages.  
  
Like:  
![Screenshot from 2024-01-10 18-42-23](https://github.com/boostorg/beast/assets/11743154/616cf21d-dc0e-4190-bd2c-4f6276c7b8c8)  
  
I'll fix them as part of this PR.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-01-10 16:00:56 UTC  
> Updated_at: 2024-01-10 19:09:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2793#issuecomment-1885137625  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2793?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`a0a8035`)](https://app.codecov.io/gh/boostorg/beast/commit/a0a80359fe8b7153c3985ccbbfb0ccf458bd634e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.01% compared to head [(`e6dcacf`)](https://app.codecov.io/gh/boostorg/beast/pull/2793?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.04%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2793/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2793?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2793      +/-   ##  
===========================================  
+ Coverage    93.01%   93.04%   +0.02%       
===========================================  
  Files          178      178                
  Lines        13688    13688                
===========================================  
+ Hits         12732    12736       +4       
+ Misses         956      952       -4       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2793?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2793?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2793/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2793?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2793?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a0a8035...e6dcacf](https://app.codecov.io/gh/boostorg/beast/pull/2793?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-01-10 17:58:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2793#issuecomment-1885350819  

An automated preview of the documentation is available at [https://2793.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2793.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---
