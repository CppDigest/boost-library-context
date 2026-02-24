# #998 - boost::math::cstdfloat::detail::float_internal128_t [Open]

> Username: gpeterhoff  
> Created at: 2023-06-20 19:39:22 UTC  
> Updated at: 2023-06-20 20:05:49 UTC  
> Url: https://github.com/boostorg/math/issues/998  

Hello,  
in boost::math::cstdfloat there are several inconsistencies with __float128/_Quad. Generally these are only available with gcc and intel. Since I don't have an intel compiler myself, you might have to modify this path; for now it should behave like the previous implementations.  
  
types <boost/math/cstdfloat/cstdfloat_types.hpp>  
intel https://community.intel.com/t5/Intel-C-Compiler/C-Compiler-128-bit-floating-point/m-p/1216949  
- WINDOWS: float_internal128_t = _quad  
- OTHER: float_internal128_t = __float128  
  
defines/constants <boost/math/cstdfloat/cstdfloat_types.hpp>  
gcc  
- the constants from <quadmath.h> or builtins can be used  
- signaling NaN is supported  
  
functions <boost/math/cstdfloat/cstdfloat_cmath.hpp>  
modified:  
- gcc: nanq does not support payload https://github.com/gcc-mirror/gcc/blob/master/libquadmath/math/nanq.c  
  --> #define BOOST_CSTDFLOAT_FLOAT128_NAN __builtin_nanq  
- hypot(x, y, z) correct result even with large values; slower than previous naive implementation  
- isfinite(x) branch-free  
  
limits <boost/math/cstdfloat/cstdfloat_limits.hpp>  
All functions can now be provided constexpr noexcept.  
  
  
future  
- branch-free isgreater, isgreaterequal, ... I don't understand the BOOST_PREVENT_MACRO_SUBSTITUTION inside the functions, otherwise I could have adjusted that right away.  
- With Matt's boost::charconv the IO functions can be provided more efficiently (operator<<, operator>>, to_string, ...).  
- It might be useful to provide quadmath as a separate sublibrary in boost::math, e.g. to avoid the #define/#undef orgies in <boost/math/cstdfloat/cstdfloat_cmath.hpp>.  
  
[cstdfloat_types.hpp.txt](https://github.com/boostorg/math/files/11805871/cstdfloat_types.hpp.txt)  
[cstdfloat_cmath.hpp.txt](https://github.com/boostorg/math/files/11805874/cstdfloat_cmath.hpp.txt)  
[cstdfloat_limits.hpp.txt](https://github.com/boostorg/math/files/11805875/cstdfloat_limits.hpp.txt)  
  
thx  
Gero

---

## Comment 1

> Username: gpeterhoff  
> Created at: 2023-06-20 20:05:48 UTC  
> Url: https://github.com/boostorg/math/issues/998#issuecomment-1599427274  

spelling error  
BOOST_CSTDFLOAT_FLOAT128_QUIET_NAN (instead of BOOST_CSTDFLOAT_FLOAT128_QUIT_NAN)
