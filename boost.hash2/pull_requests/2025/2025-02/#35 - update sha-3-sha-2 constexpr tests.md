# #35 update sha-3/sha-2 constexpr tests [Merged]

> Username: cmazakas  
> Created at: 2025-02-04 23:34:25 UTC  
> Updated at: 2025-02-06 03:07:53 UTC  
> Merged at: 2025-02-06 00:36:07 UTC  
> Closed at: 2025-02-06 00:36:07 UTC  
> Url: https://github.com/boostorg/hash2/pull/35  

Update the tests for sha-3 to better test boundary conditions. Also update shake cx tests to test result extension as well.  
  
Update sha-2 constexpr tests to include more the test vectors from the NIST.  
  
Update some of the code to avoid potential zero-sized arrays when attempting to call `update()` in constexpr code given a `char const (&str)[N]`.

---
