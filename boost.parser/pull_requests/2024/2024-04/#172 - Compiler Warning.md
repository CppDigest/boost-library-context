# #172 Compiler Warning [Merged]

> Username: d5ch4k  
> Created at: 2024-04-30 21:14:15 UTC  
> Updated at: 2024-10-01 19:06:21 UTC  
> Merged at: 2024-09-30 17:48:13 UTC  
> Closed at: 2024-09-30 17:48:13 UTC  
> Url: https://github.com/boostorg/parser/pull/172  

move declaration of 'buf' one line down into region of #if BOOST_PARSER_DETAIL_TEXT_USE_CONCEPTS as it is only used in such one.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2024-05-04 17:06:56 UTC  
> Url: https://github.com/boostorg/parser/pull/172#issuecomment-2094304604  

I'm usually happy to merge warning mitigation PRs, but this one appears to break the build.

---

## Comment 2

> Username: codecov-commenter  
> Created_at: 2024-05-04 17:24:18 UTC  
> Url: https://github.com/boostorg/parser/pull/172#issuecomment-2094313068  

## [Codecov](https://app.codecov.io/gh/tzlaine/parser/pull/172?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Zach+Laine) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 77.13%. Comparing base [(`4cea9c0`)](https://app.codecov.io/gh/tzlaine/parser/commit/4cea9c03d6baf8165a21162e66be4f99ec85b529?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Zach+Laine) to head [(`fc62dfc`)](https://app.codecov.io/gh/tzlaine/parser/pull/172?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Zach+Laine).  
  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #172   +/-   ##  
========================================  
  Coverage    77.13%   77.13%             
========================================  
  Files           29       29             
  Lines         3717     3717             
========================================  
  Hits          2867     2867             
  Misses         850      850             
```  
  
  
  
</details>  
  
[:umbrella: View full report in Codecov by Sentry](https://app.codecov.io/gh/tzlaine/parser/pull/172?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Zach+Laine).     
:loudspeaker: Have feedback on the report? [Share it here](https://about.codecov.io/codecov-pr-comment-feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Zach+Laine).

---

## Comment 3

> Username: tzlaine  
> Created_at: 2024-09-30 17:48:09 UTC  
> Url: https://github.com/boostorg/parser/pull/172#issuecomment-2383820199  

As mentioned on the other PR(s), those were old problems.  Thanks for this!

---
