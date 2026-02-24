# #201 - Resolve cyclic dependency between math and multiprecision [Closed]

> Username: jeking3  
> Created at: 2019-05-05 15:34:21 UTC  
> Updated at: 2019-09-15 18:36:11 UTC  
> Closed at: 2019-09-15 18:36:11 UTC  
> Url: https://github.com/boostorg/math/issues/201  

multiprecision depends on math:  
  
```  
multiprecision/include/boost/multiprecision/detail/big_lanczos.hpp:#include <boost/math/bindings/detail/big_lanczos.hpp>  
multiprecision/include/boost/multiprecision/detail/default_ops.hpp:#include <boost/math/policies/error_handling.hpp>  
multiprecision/include/boost/multiprecision/detail/default_ops.hpp:#include <boost/math/special_functions/fpclassify.hpp>  
multiprecision/include/boost/multiprecision/detail/default_ops.hpp:#include <boost/math/special_functions/next.hpp>  
multiprecision/include/boost/multiprecision/detail/default_ops.hpp:#include <boost/math/special_functions/hypot.hpp>  
multiprecision/include/boost/multiprecision/mpfr.hpp:#include <boost/math/special_functions/fpclassify.hpp>  
multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:#include <boost/math/special_functions/trunc.hpp>  
multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:#include <boost/math/special_functions/asinh.hpp>  
multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:#include <boost/math/special_functions/acosh.hpp>  
multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:#include <boost/math/special_functions/atanh.hpp>  
multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:#include <boost/math/special_functions/cbrt.hpp>  
multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:#include <boost/math/special_functions/expm1.hpp>  
multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:#include <boost/math/special_functions/gamma.hpp>  
multiprecision/include/boost/multiprecision/mpfi.hpp:#include <boost/math/special_functions/fpclassify.hpp>  
multiprecision/include/boost/multiprecision/mpfi.hpp:#include <boost/math/constants/constants.hpp>  
multiprecision/include/boost/multiprecision/gmp.hpp:#include <boost/math/special_functions/fpclassify.hpp>  
multiprecision/include/boost/multiprecision/gmp.hpp:#include <boost/math/special_functions/asinh.hpp>  
multiprecision/include/boost/multiprecision/gmp.hpp:#include <boost/math/special_functions/acosh.hpp>  
multiprecision/include/boost/multiprecision/gmp.hpp:#include <boost/math/special_functions/atanh.hpp>  
multiprecision/include/boost/multiprecision/gmp.hpp:#include <boost/math/special_functions/cbrt.hpp>  
multiprecision/include/boost/multiprecision/gmp.hpp:#include <boost/math/special_functions/expm1.hpp>  
multiprecision/include/boost/multiprecision/gmp.hpp:#include <boost/math/special_functions/gamma.hpp>  
multiprecision/include/boost/multiprecision/concepts/mp_number_archetypes.hpp:#include <boost/math/special_functions/fpclassify.hpp>  
multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:#include <boost/math/policies/policy.hpp>  
multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:#include <boost/math/special_functions/asinh.hpp>  
multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:#include <boost/math/special_functions/acosh.hpp>  
multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:#include <boost/math/special_functions/atanh.hpp>  
multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:#include <boost/math/special_functions/cbrt.hpp>  
multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:#include <boost/math/special_functions/expm1.hpp>  
multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:#include <boost/math/special_functions/gamma.hpp>  
multiprecision/include/boost/multiprecision/tommath.hpp:#include <boost/math/special_functions/fpclassify.hpp>  
```  
  
math depends on multiprecision (it actually depends on the internals of multiprecision):  
```  
math/include/boost/math/tools/signal_statistics.hpp:#include <boost/multiprecision/detail/number_base.hpp>  
math/include/boost/math/tools/norms.hpp:#include <boost/multiprecision/detail/number_base.hpp>  
math/include/boost/math/tools/bivariate_statistics.hpp:#include <boost/multiprecision/detail/number_base.hpp>  
math/include/boost/math/tools/univariate_statistics.hpp:#include <boost/multiprecision/detail/number_base.hpp>  
math/include/boost/math/tools/roots.hpp:#include <boost/multiprecision/detail/number_base.hpp> // test for multiprecision types.  
math/include_private/boost/math/constants/generate.hpp:#include <boost/multiprecision/cpp_dec_float.hpp>  
```  
  
Direct dependency cycles should be avoided to prepare boost for a future where each repository releases independently and depends on versioning to resolve module dependencies.

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-05-05 16:35:16 UTC  
> Url: https://github.com/boostorg/math/issues/201#issuecomment-489442123  

hmm, I'm not sure how to fix this. The idea was that we'd make those algorithms more generic by letting them work on both real and complex types. So you need to have some type detection logic, and a very useful type to detect is the multiprecision types.  
  
IIRC it could not be fixed with SFINAE; the operations on the real branch were defined on both complex and real types, but the complex results were meaningless.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-05-05 18:12:12 UTC  
> Url: https://github.com/boostorg/math/issues/201#issuecomment-489449716  

We have a common problem here: where do we put "glue code" that allows library X to interoperate with library Y.  I raised this on the list when a modularised boost was first suggested, Robert Ramey has raised it again recently, and I still don't see a good solution?

---

## Comment 3

> Username: jeking3  
> Created at: 2019-05-06 13:05:31 UTC  
> Url: https://github.com/boostorg/math/issues/201#issuecomment-489613026  

Typically both of the cross dependencies are moved into another library that both can depend on, removing the need to depend on each-other.  There's a similar discussion in a lexical_cast issue as well.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-09-15 18:36:11 UTC  
> Url: https://github.com/boostorg/math/issues/201#issuecomment-531588857  

This is now fixed in develop (which is to say math does not directly depend on multiprecision).  
  
Note that the use under include_private/ remains since this directory is purely for testing and maintenance, and not part of the library's public interface.
