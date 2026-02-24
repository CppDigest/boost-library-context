# #91 - cond vs code comparison not visible from error_condition [Closed]

> Username: nUl1  
> Created at: 2022-08-23 10:46:45 UTC  
> Updated at: 2022-08-24 23:27:14 UTC  
> Closed at: 2022-08-24 23:27:14 UTC  
> Url: https://github.com/boostorg/system/issues/91  

Hello,  
  
Commit 9fdfa6c645f0d1f1060e1a0f93f456314f992107 moved comparison operators from namespace into error_code as a friend functions. This made them invisible to name lookup from `error_condition` when `error_code` does not participate directly in the comparison.  
  
As a result, comparing an `error_condition` to a `boost::system::error_code` enumeration does not work anymore. At the same time comparison with  `std::error_code` enumerations works because these operators are declared as `error_condition` friends (the same problem stands with `std::error_code` vs `error_condition` enum though).  
  
Example: https://godbolt.org/z/5qbPesvnz  
  
It looks like some forward declarations for comparison operators outside classes would allow for name lookup to work and will fix the issue, but I'm not sure where is the right place to put them.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-08-23 11:15:36 UTC  
> Url: https://github.com/boostorg/system/issues/91#issuecomment-1223920727  

I agree that this is a regression, even though I can't think of a legitimate use case for comparing conditions against error code enums.
