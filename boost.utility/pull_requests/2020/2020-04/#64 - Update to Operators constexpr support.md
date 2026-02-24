# #64 Update to Operators constexpr support  [Merged]

> Username: glenfe  
> Created at: 2020-04-12 17:13:24 UTC  
> Updated at: 2020-04-12 17:33:18 UTC  
> Merged at: 2020-04-12 17:33:17 UTC  
> Closed at: 2020-04-12 17:33:17 UTC  
> Url: https://github.com/boostorg/utility/pull/64  

The following changes:  
- Use a separate compile-only test for `constexpr` support so that any defects relating to `constexpr` do not impact the normal operators tests. Use `BOOST_STATIC_ASSERT` in the tests to avoid any issues with implementation with a defective `static_assert`.  
- Rename `BOOST_OPS_CONSTEXPR` to `BOOST_OPERATORS_CONSTEXPR` to not introduce a new macro convention into the mix.  
- Simplify the definition of `BOOST_OPERATORS_CONSTEXPR` with `BOOST_WORKAROUND`.

---
