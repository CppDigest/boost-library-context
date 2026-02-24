# #50 - Signed integer overflow in scan_arbitrary.hpp [Open]

> Username: goatyde  
> Created at: 2020-08-13 13:03:25 UTC  
> Updated at: 2020-08-13 13:03:25 UTC  
> Url: https://github.com/boostorg/polygon/issues/50  

Undefined behaviour sanitizer on Linux with Clang complains about this:  
  
When "Unit" is int, a call to validate_scan in Line 148 causes a "std::numeric_limits<Unit>::min -1" in line 174
