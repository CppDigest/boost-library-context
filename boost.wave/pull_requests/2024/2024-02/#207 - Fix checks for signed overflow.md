# #207 Fix checks for signed overflow [Merged]

> Username: jefftrull  
> Created at: 2024-02-02 21:30:51 UTC  
> Updated at: 2024-06-26 04:53:44 UTC  
> Merged at: 2024-06-26 04:53:44 UTC  
> Closed at: 2024-06-26 04:53:44 UTC  
> Url: https://github.com/boostorg/wave/pull/207  

When verifying that code being preprocessed does not invoke undefined behavior, Wave does it itself.  
This change performs tests on the operands in advance, instead.  
  
Tests `t_6_15`, `t_6_17`, and `t_6_18` (though not `t_6_17`, for some reason) signal errors in the absence of this fix with `-fsanitize=signed-integer-overflow` configured, but it does not cause the tests to fail, because these tests are expected to exit with an error anyway.  
  
If merged, this will fix #197

---

## Comment 1

> Username: jefftrull  
> Created_at: 2024-06-25 22:05:02 UTC  
> Url: https://github.com/boostorg/wave/pull/207#issuecomment-2190049933  

While reviewing this old PR I realized that we don't handle `INT_MIN / -1` properly so I've added that.

---
