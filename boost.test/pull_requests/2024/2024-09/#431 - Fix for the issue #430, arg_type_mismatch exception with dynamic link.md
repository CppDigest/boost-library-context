# #431 Fix for the issue #430, arg_type_mismatch exception with dynamic link [Merged]

> Username: kantan2015  
> Created at: 2024-09-24 23:39:38 UTC  
> Updated at: 2024-10-04 17:50:39 UTC  
> Merged at: 2024-10-04 17:50:39 UTC  
> Closed at: 2024-10-04 17:50:39 UTC  
> Url: https://github.com/boostorg/test/pull/431  

This is the fix for https://github.com/boostorg/test/issues/430.  
If the library is linked dynamically, clang built binary raises the arg_type_mismatch exception on reading configuration variables with type output_format or report_level.  
I've added the test in unit_test_example_16.cpp but found that it was commented out from build and leave as it is.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2024-10-04 17:50:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/test/pull/431#pullrequestreview-2348761813  

Looks good to me. Thank you!

---
