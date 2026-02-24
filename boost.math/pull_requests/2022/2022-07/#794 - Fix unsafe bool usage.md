# #794 Fix unsafe bool usage [Merged]

> Username: mborland  
> Created at: 2022-07-02 00:13:26 UTC  
> Updated at: 2022-07-03 22:44:22 UTC  
> Merged at: 2022-07-03 22:44:19 UTC  
> Closed at: 2022-07-03 22:44:19 UTC  
> Url: https://github.com/boostorg/math/pull/794  

Replace unsafe bool usage in type promotion with `boost::math::tools::promote_args_t`. Additionally, refactor sonarlint warnings.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-07-02 11:27:56 UTC  
> Url: https://github.com/boostorg/math/pull/794#issuecomment-1172882742  

Question: do we want to remove the inline and noexcept qualifiers?  Is a likely use case that this may be used in code which is maybe or maybe not constexpr?  If not then maybe we should perhaps devise a static_assert that will fail when instantiated in a non-constexpr context, and/or make the qualifiers `consteval` in C++20 and later?

---

## Comment 2

> Username: mborland  
> Created_at: 2022-07-02 14:47:31 UTC  
> Url: https://github.com/boostorg/math/pull/794#issuecomment-1172910393  

The `inline` qualifier was removed because [it is redundant](https://rules.sonarsource.com/cpp/RSPEC-5408). The `noexcept` qualifier was removed because at compile time the function is `noexcept`, but at runtime the STL does not require it. This matches the now [accepted C++23 proposal](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2021/p0533r8.pdf) for making some of cmath constexpr.

---
