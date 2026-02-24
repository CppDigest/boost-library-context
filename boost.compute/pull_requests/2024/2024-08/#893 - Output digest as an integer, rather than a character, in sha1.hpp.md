# #893 Output digest[i] as an integer, rather than a character, in sha1.hpp [Merged]

> Username: pdimov  
> Created at: 2024-08-22 07:23:49 UTC  
> Updated at: 2024-08-22 14:55:13 UTC  
> Merged at: 2024-08-22 14:55:12 UTC  
> Closed at: 2024-08-22 14:55:12 UTC  
> Url: https://github.com/boostorg/compute/pull/893  

The fix in https://github.com/boostorg/compute/pull/887 is slightly incorrect, because `digest[i]` is `unsigned char` and is output as a character to `buf`. This changes it to be output as an integer, as intended.  
  
It makes the test added in https://github.com/boostorg/compute/pull/892 pass.

---
