# #162 [develop] Fix undefined behavior in ../support/detail/endian/endian.hpp (#11726) [Merged]

> Username: davidlt  
> Created at: 2015-10-20 12:30:28 UTC  
> Updated at: 2015-10-20 14:30:12 UTC  
> Merged at: 2015-10-20 14:30:12 UTC  
> Closed at: 2015-10-20 14:30:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/162  

Undefined behavior sanitizer (UBSan) with GCC 5.1.1 complains about left  
shifting of negative value at run-time. This happens once  
boost::spirit::detail::load_little_endian is used with a singed type.  
  
The patch resolves the issue by using unsigned type for making left shifts,  
then load_little_endian casts it to specific type T.  
  
Tested with Clang and GCC locally (test case in #11726) and all worked fine.  
Tested by running all Boost test cases before and after, no regressions.  
  
Signed-off-by: David Abdurachmanov david.abdurachmanov@gmail.com

---
