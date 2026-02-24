# #44 Use `is_same` from Boost.TypeTraits [Closed]

> Username: Lastique  
> Created at: 2022-12-22 21:59:18 UTC  
> Updated at: 2022-12-23 11:31:18 UTC  
> Closed at: 2022-12-23 02:13:17 UTC  
> Url: https://github.com/boostorg/function/pull/44  

`boost::core::is_same` is deprecated, so use the one from Boost.TypeTraits.

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-12-22 22:47:26 UTC  
> Url: https://github.com/boostorg/function/pull/44#issuecomment-1363414627  

Re. GitHub Actions CI failures with CMake, it doesn't look like these are caused by my changes, and I don't understand why it can't find Boost.Describe target.

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-12-22 23:10:10 UTC  
> Url: https://github.com/boostorg/function/pull/44#issuecomment-1363426384  

I'll look into the GHA failures.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-12-23 02:13:17 UTC  
> Url: https://github.com/boostorg/function/pull/44#issuecomment-1363544970  

I decided to use BOOST_TEST_TRAIT_SAME instead: https://github.com/boostorg/function/commit/53c084084d05b1340acf9fd2902885f14e3eb324.

---
