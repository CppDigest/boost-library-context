# #399 Fix overflow warning in `print_helper.hpp`. #398 [Merged]

> Username: JBouwer  
> Created at: 2023-11-13 23:41:53 UTC  
> Updated at: 2024-01-09 08:59:25 UTC  
> Merged at: 2024-01-09 08:59:25 UTC  
> Closed at: 2024-01-09 08:59:25 UTC  
> Url: https://github.com/boostorg/test/pull/399  

- (Implicitly) printing a big enough type results in: "warning: overflow in expression; result is 2147483347 with type 'int' [-Winteger-overflow]".  
- Fixed by promoting the calculation to 'std::streamsize'.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2023-11-14 07:40:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/test/pull/399#pullrequestreview-1729082381  

LGTM. I approved the CI run as well.

---
