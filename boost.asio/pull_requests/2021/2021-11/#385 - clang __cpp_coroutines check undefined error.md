# #385 clang __cpp_coroutines check undefined error [Open]

> Username: ksherlock  
> Created at: 2021-11-14 18:46:34 UTC  
> Updated at: 2021-11-16 06:45:08 UTC  
> Url: https://github.com/boostorg/asio/pull/385  

With `-Werror` and `-Wundef`, the `clang` `__cpp_coroutines` check can cause an error.  (error:   
      '__cpp_coroutines' is not defined, evaluates to 0 [-Werror,-Wundef]) Presumably the gcc `__cpp_impl_coroutine` checks would also suffer from this issue.  
  
Error encountered with Apple clang version 11.0.0 (clang-1100.0.33.17)

---

## Comment 1

> Username: viccpp  
> Created_at: 2021-11-16 06:44:45 UTC  
> Updated_at: 2021-11-16 06:45:08 UTC  
> Url: https://github.com/boostorg/asio/pull/385#issuecomment-969925841  

This style is recommended by the Committee - https://isocpp.org/std/standing-documents/sd-6-sg10-feature-test-recommendations. So I think usage of `-Wundef` + `-Werror` is a bad idea in general

---
