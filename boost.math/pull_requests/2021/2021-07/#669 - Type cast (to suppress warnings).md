# #669 Type cast (to suppress warnings) [Merged]

> Username: kojiynet  
> Created at: 2021-07-25 10:14:20 UTC  
> Updated at: 2021-07-26 01:18:47 UTC  
> Merged at: 2021-07-25 18:29:58 UTC  
> Closed at: 2021-07-25 18:29:58 UTC  
> Url: https://github.com/boostorg/math/pull/669  

When means_and_covariance_seq_impl() and  correlation_coefficient_seq_impl() try to return a tuple, the type of "i" is assume to be Real, while actually its type is size_t. This makes VC issue warnings. To suppress this we might want to write "Real(i)" instead of "i".  
This is also consistent with "Real(0)" and "Real(1)" in some of these return statements.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-07-25 18:30:02 UTC  
> Url: https://github.com/boostorg/math/pull/669#issuecomment-886240530  

Thanks!

---

## Comment 2

> Username: kojiynet  
> Created_at: 2021-07-26 01:18:47 UTC  
> Url: https://github.com/boostorg/math/pull/669#issuecomment-886302762  

Great, thanks!

---
