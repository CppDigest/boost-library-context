# #128 Use improved fall back routine for 80 and 128 bit from chars [Merged]

> Username: mborland  
> Created at: 2024-01-22 07:41:26 UTC  
> Updated at: 2024-01-22 08:23:25 UTC  
> Merged at: 2024-01-22 08:23:23 UTC  
> Closed at: 2024-01-22 08:23:23 UTC  
> Url: https://github.com/boostorg/charconv/pull/128  

Also test the position of the pointer since we had a dangling pointer in the replaced code.  
Closes: #121

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-22 08:20:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/128#issuecomment-1903470662  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`6b19ef3`)](https://app.codecov.io/gh/cppalliance/charconv/commit/6b19ef3262ba432fa1f91c942bddd9c6dbb3c64e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.25% compared to head [(`14a1a0e`)](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.24%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/128/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #128      +/-   ##  
===========================================  
- Coverage    88.25%   88.24%   -0.01%       
===========================================  
  Files           55       55                
  Lines         7893     7888       -5       
===========================================  
- Hits          6966     6961       -5       
  Misses         927      927                
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [src/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `97.91% <100.00%> (+1.48%)` | :arrow_up: |  
| [test/roundtrip.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yb3VuZHRyaXAuY3Bw) | `98.42% <100.00%> (ø)` | |  
| [test/test\_float128.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/cppalliance/charconv/pull/128/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [6b19ef3...14a1a0e](https://app.codecov.io/gh/cppalliance/charconv/pull/128?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
