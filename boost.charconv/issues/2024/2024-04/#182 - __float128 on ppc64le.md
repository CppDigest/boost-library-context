# #182 - __float128 on ppc64le [Closed]

> Username: iv-m  
> Created at: 2024-04-16 12:10:28 UTC  
> Updated at: 2024-04-23 09:10:15 UTC  
> Closed at: 2024-04-22 07:10:57 UTC  
> Url: https://github.com/boostorg/charconv/issues/182  

I'm trying to compile boost 1.85.0 with gcc 13.2.1 on ppc64le machine that does not have a 'real' long double:  
  
```  
LDBL_MANT_DIG: 106  
LDBL_MAX_EXP: 1024  
```  
  
But on this system libquadmath is available and `__float128` seems to work.  
  
I'm getting the following error:  
  
```  
[...]  
libs/charconv/build/../src/from_chars.cpp: In function 'boost::charconv::from_chars_result boost::charconv::from_chars_erange(const char*, const char*, __ieee128&, chars_format)':  
libs/charconv/build/../src/from_chars.cpp:120:48: error: 'compute_float128' is not a member of 'boost::charconv::detail'; did you mean 'compute_float32'?  
  120 |     auto return_val = boost::charconv::detail::compute_float128(exponent, significand, sign, success);  
      |                                                ^~~~~~~~~~~~~~~~  
      |                                                compute_float32  
libs/charconv/build/../src/from_chars.cpp: In function 'boost::charconv::from_chars_result boost::charconv::from_chars_erange(const char*, const char*, long double&, chars_format)':  
libs/charconv/build/../src/from_chars.cpp:258:48: error: 'compute_float80' is not a member of 'boost::charconv::detail'; did you mean 'compute_float64'?  
  258 |     auto return_val = boost::charconv::detail::compute_float80<long double>(exponent, significand, sign, success);  
      |                                                ^~~~~~~~~~~~~~~  
      |                                                compute_float64  
[...]  
```

---

## Comment 1

> Username: iv-m  
> Created at: 2024-04-16 12:13:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/182#issuecomment-2058944705  

It looks like `BOOST_CHARCONV_LDBL_BITS` is not defined on this system, but `BOOST_CHARCONV_HAS_FLOAT128` is. So `boost/charconv/detail/compute_float80.hpp` is not included by `from_chars.cpp`, but `from_chars_erange` overload for `__float128` is not preprocessed away.

---

## Comment 2

> Username: mborland  
> Created at: 2024-04-16 12:32:42 UTC  
> Url: https://github.com/boostorg/charconv/issues/182#issuecomment-2058980631  

Are you trying to convert that long double type (__ibm128)? From the docs: Long doubles can be 64, 80, or 128-bit, but must be IEEE 754 compliant. An example of a non-compliant, and therefore unsupported, format is `__ibm128`.

---

## Comment 3

> Username: mborland  
> Created at: 2024-04-16 12:45:48 UTC  
> Url: https://github.com/boostorg/charconv/issues/182#issuecomment-2059007926  

Actually if you are trying with `__float128` can you try latest develop? I reworked `__float128` and quadmath support since release freeze: https://github.com/boostorg/charconv/pull/177

---

## Comment 4

> Username: pdimov  
> Created at: 2024-04-16 12:47:56 UTC  
> Url: https://github.com/boostorg/charconv/issues/182#issuecomment-2059012096  

I don't think he's trying to use `long double` or `__float128`, the library just fails to build.

---

## Comment 5

> Username: mborland  
> Created at: 2024-04-16 13:01:38 UTC  
> Url: https://github.com/boostorg/charconv/issues/182#issuecomment-2059039434  

> I don't think he's trying to use `long double` or `__float128`, the library just fails to build.  
  
I was able to replicate on develop with `-mabi=ibmlongdouble -mlong-double-128` on fedora 39. I forgot that it had transitioned to `__float128`: https://developers.redhat.com/articles/2023/05/16/benefits-fedora-38-long-double-transition-ppc64le#

---

## Comment 6

> Username: iv-m  
> Created at: 2024-04-16 14:16:23 UTC  
> Url: https://github.com/boostorg/charconv/issues/182#issuecomment-2059205792  

> I was able to replicate on develop with `-mabi=ibmlongdouble -mlong-double-128` on fedora 39. I forgot that it had transitioned to `__float128`: https://developers.redhat.com/articles/2023/05/16/benefits-fedora-38-long-double-transition-ppc64le#  
  
Yup, Fedora transitioned to `ieeelongdouble`,  but I'm not using Fedora, so we still have `ibmlongdouble` as the default.

---

## Comment 7

> Username: mborland  
> Created at: 2024-04-18 06:10:36 UTC  
> Url: https://github.com/boostorg/charconv/issues/182#issuecomment-2063074214  

@iv-m can you please give https://github.com/boostorg/charconv/pull/183 a try? Everything seemed fine on QEMU Fedora 39 with both long double ABIs, but I want to make sure there's not some difference from emulation.

---

## Comment 8

> Username: iv-m  
> Created at: 2024-04-22 10:46:36 UTC  
> Url: https://github.com/boostorg/charconv/issues/182#issuecomment-2069075260  

Sorry for the delay, my ppc64le test environment was broken and it took some time to rebuild it.  
  
Current develop branch (100473b1dc3c3dfff0513e584c8f447824c7e34c, with #183 merged)  works for me when explicitly specifying C++ standard -- for example, the following command run from the top of the boost tree completes successfully:  
  
```  
b2 cxxstd=11 charconv  
```  
  
But when I don't specify `cxxstd`, the default is used, which for my compiler is `gnu++17`. This enables GNU extensions, including `__float128` and `Q` literals. Their presence is correctly detected by the build system, and the compilation fails:  
  
  
```  
In file included from libs/charconv/build/../src/to_chars.cpp:8:  
libs/charconv/build/../src/to_chars_float_impl.hpp: In function 'boost::charconv::to_chars_result boost::charconv::detail::to_chars_hex(char*, char*, Real, int)':  
libs/charconv/build/../src/to_chars_float_impl.hpp:277:140: error: 'ieee754_binary80' was not declared in this scope; did you mean 'ieee754_binary64'?  
  277 |                     typename std::conditional<std::is_same<Real, __float128>::value || BOOST_CHARCONV_LDBL_BITS == 128, ieee754_binary128, ieee754_binary80>::type  
      |                                                                                                                                            ^~~~~~~~~~~~~~~~  
      |                                                                                                                                            ieee754_binary64  
libs/charconv/build/../src/to_chars_float_impl.hpp:277:156: error: template argument 3 is invalid  
  277 |                     typename std::conditional<std::is_same<Real, __float128>::value || BOOST_CHARCONV_LDBL_BITS == 128, ieee754_binary128, ieee754_binary80>::type  
      |                                                                                                                                                            ^  
libs/charconv/build/../src/to_chars_float_impl.hpp:277:157: error: '<declaration error>' is not a template [-fpermissive]  
  277 |                     typename std::conditional<std::is_same<Real, __float128>::value || BOOST_CHARCONV_LDBL_BITS == 128, ieee754_binary128, ieee754_binary80>::type  
      |                                                                                                                                                             ^~  
libs/charconv/build/../src/to_chars_float_impl.hpp:285:13: error: template argument 3 is invalid  
  285 |             >::type>::type  
      |             ^  
libs/charconv/build/../src/to_chars_float_impl.hpp:285:14: error: '<declaration error>' is not a template [-fpermissive]  
  285 |             >::type>::type  
      |              ^~  
libs/charconv/build/../src/to_chars_float_impl.hpp:285:20: error: template argument 3 is invalid  
  285 |             >::type>::type  
      |                    ^  
libs/charconv/build/../src/to_chars_float_impl.hpp:285:21: error: '<declaration error>' is not a template [-fpermissive]  
  285 |             >::type>::type  
      |                     ^~  
libs/charconv/build/../src/to_chars_float_impl.hpp:302:67: error: 'type_layout' has not been declared  
  302 |     const Unsigned_Integer denorm_mask = (Unsigned_Integer(1) << (type_layout::significand_bits)) - 1;  
      |                                                                   ^~~~~~~~~~~  
libs/charconv/build/../src/to_chars_float_impl.hpp:304:61: error: 'type_layout' has not been declared  
  304 |     std::uint64_t exponent = extract_exp(value, uint_value, type_layout::significand_bits);  
      |                                                             ^~~~~~~~~~~  
libs/charconv/build/../src/to_chars_float_impl.hpp:365:33: error: 'type_layout' has not been declared  
  365 |         unbiased_exponent = 1 + type_layout::exponent_bias;  
      |                                 ^~~~~~~~~~~  
libs/charconv/build/../src/to_chars_float_impl.hpp:370:40: error: 'type_layout' has not been declared  
  370 |         unbiased_exponent = exponent + type_layout::exponent_bias;  
      |                                        ^~~~~~~~~~~  
```

---

## Comment 9

> Username: mborland  
> Created at: 2024-04-23 09:10:13 UTC  
> Url: https://github.com/boostorg/charconv/issues/182#issuecomment-2071803521  

Unfortunately I am unable to replicate that. Running the tests with `b2 cxxstd=11 cxxflags="-mabi=ibmlongdouble -mlong-double-128"` and with `b2 cxxstd=11 cxxstd-dialect=gnu cxxflags="-mabi=ibmlongdouble -mlong-double-128"` both pass the entire test suite with the latter correctly detecting support for quadmath.
