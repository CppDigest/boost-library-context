# #1285 - GPU compilation failure with pow.hpp from boost::multiprecision [Open]

> Username: gtkramer  
> Created at: 2025-07-04 02:05:38 UTC  
> Updated at: 2025-07-04 19:54:27 UTC  
> Url: https://github.com/boostorg/math/issues/1285  

I have some C++ code that compiled just fine with `nvcc` from CUDA 12.6.1 and Boost 1.84.0.  After updating to Boost 1.88.0, which includes the GPU math changes from Boost 1.87.0, I'm now seeing the following error message:  
  
```  
/usr/include/boost/multiprecision/detail/functions/pow.hpp(789): error: namespace "boost::math" has no member "rounding_error"  
     catch(const boost::math::rounding_error&)  
                              ^  
```  
  
If I'm understanding Boost's macro and include header structure correctly, boost/multiprecision/detail/functions/pow.hpp is inlined with boost/multiprecision/detail/default_ops.hpp.  pow.hpp is always included no matter what in default_ops.hpp.  `boost::math::rounding_error` is defined in boost/math/policies/error_handling.hpp, which is only included by default_ops.hpp if `BOOST_MP_MATH_AVAILABLE` is defined.  This is the same macro that's used for guarding `BOOST_MP_CATCH(const boost::math::rounding_error&) {}` in pow.hpp (the same code not compiling), so all seems good.  But, `boost::math::rounding_error` is only defined when `BOOST_MATH_NO_EXCEPTIONS` is NOT defined.  And, when compiling with `nvcc`, `BOOST_MATH_NO_EXCEPTIONS` DOES get defined, according to boost/math/tools/config.hpp, because we've got `__CUDACC__` defined by simply using `nvcc`.  
  
Should we be adjusting pow.hpp, and possibly any other cases like this in either `boost::math` or `boost::multiprecision`, to have something like this instead?  
  
```  
#if defined(BOOST_MP_MATH_AVAILABLE) && !defined(BOOST_MATH_NO_EXCEPTIONS)  
BOOST_MP_CATCH(const boost::math::rounding_error&)  
{ /* Fallthrough */  
}  
#endif  
```  
  
That way we only define this catch condition when we have exception support and multiprecision math is available?

---

## Comment 1

> Username: gtkramer  
> Created at: 2025-07-04 02:24:07 UTC  
> Url: https://github.com/boostorg/math/issues/1285#issuecomment-3034223153  

I can confirm this was enough to get our code compiling again, but I'm guessing this could be a more widespread issue than just this one instance.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2025-07-04 07:53:16 UTC  
> Url: https://github.com/boostorg/math/issues/1285#issuecomment-3034879460  

Yes, we need to check for BOOST_NO_EXCEPTIONS as well.  I don't think multiprecision is well tested in that mode.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2025-07-04 09:40:18 UTC  
> Url: https://github.com/boostorg/math/issues/1285#issuecomment-3035226108  

> Yes, we need to check for `BOOST_NO_EXCEPTIONS` as well. I don't think multiprecision is well tested in that mode.  
  
I'm actually in the process of expanding Multiprecision's tests for 1.91 (the release in November). We can discuss testing improvements in a separate, future ticket.

---

## Comment 4

> Username: mborland  
> Created at: 2025-07-04 14:16:33 UTC  
> Url: https://github.com/boostorg/math/issues/1285#issuecomment-3036450742  

I think this is similar to https://github.com/boostorg/multiprecision/issues/695 which is fixed on develop.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-07-04 16:38:29 UTC  
> Url: https://github.com/boostorg/math/issues/1285#issuecomment-3036852317  

BTW I'm curious as to whether you're using multiprecision on a CUDA device?  Interesting if so ;)

---

## Comment 6

> Username: gtkramer  
> Created at: 2025-07-04 19:49:00 UTC  
> Updated at: 2025-07-04 19:54:27 UTC  
> Url: https://github.com/boostorg/math/issues/1285#issuecomment-3037148246  

It seems like `BOOST_MP_CATCH` already checks for `BOOST_NO_EXCEPTIONS`, which explains why we've got this macro to begin with.  I guess only cases outside of the std library are affected here during preprocessing, such as with `boost::math::rounding_error`, in the multiprocessing module.

---

## Comment 7

> Username: gtkramer  
> Created at: 2025-07-04 19:53:14 UTC  
> Url: https://github.com/boostorg/math/issues/1285#issuecomment-3037153160  

> BTW I'm curious as to whether you're using multiprecision on a CUDA device? Interesting if so ;)  
  
I don't think we are intentionally XD.  We have a complex code base where we are using boost/multiprecision/cpp_int.hpp mostly for the CPU, but we extended a part of it to compile with the GPU.  This part got caught on the GPU compilation path due to dependencies and happened to expose an edge case it seems.
