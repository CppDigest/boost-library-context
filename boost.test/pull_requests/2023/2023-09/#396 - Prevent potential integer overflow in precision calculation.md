# #396 Prevent potential integer overflow in precision calculation. [Closed]

> Username: tinko92  
> Created at: 2023-09-30 09:00:28 UTC  
> Updated at: 2024-01-09 08:59:58 UTC  
> Closed at: 2024-01-09 08:59:57 UTC  
> Url: https://github.com/boostorg/test/pull/396  

This changes the precision calculation in print_helper so that the current approximation is used if the number of digits is such that no integer overflow can occur in the multiplication and a cruder approximation that cannot overflow is used otherwise. As far as I can see this shouldn't change anything for built-in and other reasonably small types. My motivation for this change is an integer overflow warning in the Boost.Geometry test suite for a test with a Boost.Multiprecision type.  
  
Note that, if I read 3.1 in https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2006/n2005.pdf right, the 301/1000 approximation of log(2) might be insufficient for types with more 265 digits. If this is considered relevant for the use case of this code and warrants the additional complexity, I can change this PR to account for that.

---

## Comment 1

> Username: mborland  
> Created_at: 2024-01-09 08:59:57 UTC  
> Url: https://github.com/boostorg/test/pull/396#issuecomment-1882652926  

Fixed by: https://github.com/boostorg/test/pull/399

---
