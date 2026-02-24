# #659 Fix build on platforms with "double-double" long double type [Merged]

> Username: mscastanho  
> Created at: 2021-07-07 19:59:38 UTC  
> Updated at: 2021-07-13 08:13:31 UTC  
> Merged at: 2021-07-13 08:13:31 UTC  
> Closed at: 2021-07-13 08:13:31 UTC  
> Url: https://github.com/boostorg/math/pull/659  

Since commit 01f3b7d7721fd most tests started to fail on platforms that use a  
"double-double" for long double, like powerpc64le:  
  
```  
../../../boost/math/tools/precision.hpp:148:137: error: expected primary-expression before ‘long’  
  148 | inline constexpr long double epsilon<long double>(const std::true_type&  
  BOOST_MATH_APPEND_EXPLICIT_TEMPLATE_TYPE(long double)) noexcept(long double)  
  
../../../boost/math/tools/precision.hpp:148:137: error: expected ‘)’ before ‘long’  
  148 | inline constexpr long double epsilon<long double>(const std::true_type&  
  BOOST_MATH_APPEND_EXPLICIT_TEMPLATE_TYPE(long double)) noexcept(long double)  
  
../../../boost/math/tools/precision.hpp:148:137: error: expected initializer before ‘long’  
  148 | inline constexpr long double epsilon<long double>(const std::true_type&  
  BOOST_MATH_APPEND_EXPLICIT_TEMPLATE_TYPE(long double)) noexcept(long double)  
```  
  
Fix this by replacing the noexcept expression with the contents of  
`BOOST_MATH_NOEXCEPT`, which was the intention of commit 01f3b7d7721fd.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2021-07-09 17:32:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/659#pullrequestreview-703262226  

@jzmaddock This change looks good to me; fixes an error that I introduced.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-07-09 18:09:06 UTC  
> Url: https://github.com/boostorg/math/pull/659#issuecomment-877366286  

Hi @mscastanho this is nice. Thanks for investigating this phenomenon.  
  
@mborland and @jzmaddock I took the liberty of approving the CI run.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-07-09 18:10:52 UTC  
> Url: https://github.com/boostorg/math/pull/659#issuecomment-877367326  

Added note. We are still a long ways away, but work to implement double-double BSL backend is underway in the 2021 GSoC [here](https://github.com/BoostGSoC21/multiprecision)

---

## Comment 4

> Username: mborland  
> Created_at: 2021-07-12 19:02:39 UTC  
> Url: https://github.com/boostorg/math/pull/659#issuecomment-878519567  

This looks good to go. Should probably make it into #661.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-07-13 08:13:22 UTC  
> Url: https://github.com/boostorg/math/pull/659#issuecomment-878876338  

Being somewhat pedantic: `std::is_floating_point<long double>::value` is necessarily always true.  Merging anyhow.

---
