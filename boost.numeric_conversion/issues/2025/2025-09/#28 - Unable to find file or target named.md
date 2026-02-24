# #28 - Unable to find file or target named [Closed]

> Username: sdarwin  
> Created at: 2025-09-12 14:52:45 UTC  
> Updated at: 2025-09-23 11:49:52 UTC  
> Closed at: 2025-09-23 11:49:52 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/28  

Regression tests are failing and this correlates to the most recent commit of `boostorg/numeric_conversion`. Any ideas?     
  
https://app.circleci.com/pipelines/github/boostorg/regression  
  
```  
error: Unable to find file or target named  
error:     '/boost/conversion//boost_conversion'  
error: referred to from project at  
error:     '/root/project/boost-reports/boost_root/libs/numeric/conversion'  
```  
  
@grafikrobot , @pdimov

---

## Comment 1

> Username: pdimov  
> Created at: 2025-09-12 15:22:31 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/28#issuecomment-3285710361  

You probably need to add `conversion` here  
  
https://github.com/boostorg/regression/blob/1c8d593b05b2788a54806b07b6365894873ed5cb/ci_build_results_all.sh#L87  
  
It's not clear what changed in order to require it; last successful run was 3 month ago so it's hard to say.

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-09-12 15:33:20 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/28#issuecomment-3285745765  

Sent a PR

---

## Comment 3

> Username: sdarwin  
> Created at: 2025-09-23 11:49:52 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/28#issuecomment-3323671334  

Per the above recommendation, fixed.
