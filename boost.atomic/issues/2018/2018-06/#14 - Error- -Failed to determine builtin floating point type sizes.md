# #14 - Error: "Failed to determine builtin floating point type sizes" [Closed]

> Username: ClundXIII  
> Created at: 2018-06-29 18:36:24 UTC  
> Updated at: 2023-08-22 09:45:13 UTC  
> Closed at: 2019-03-10 18:14:48 UTC  
> Url: https://github.com/boostorg/atomic/issues/14  

Hi,  
  
I received an error telling me to make a bug report here ;)  
I am using GCC 8.1.1 on OpenSUSE Tumbleweed. The project itself is a huge chunk of cmake spaghetti code so I used a quick `@echo` to print out the exact command:  
  
```  
/usr/bin/g++ -frounding-math -std=c++11 -Wuninitialized -Wno-error=unused-variable -DBOOST_NO_AUTO_PTR -DBOOST_SYSTEM_NO_DEPRECATED=1 -fopenmp -Wall -ftemplate-depth-100 -frounding-math -Wno-misleading-indentation -Wno-placement-new -Wno-expansion-to-defined -Wno-long-long -Wno-unknown-pragmas -Wno-comment -Wno-address -Wno-error=address -Wno-expansion-to-defined -g  
```  
  
The error I was talking of:  
  
```  
/.../boost_gcc_debug/include/boost/atomic/detail/float_sizes.hpp:139:2: error: #error Boost.Atomic: Failed to determine builtin floating point type sizes, the target platform is not supported. Please, report to the developers (patches are welcome).  
  
 #error Boost.Atomic: Failed to determine builtin floating point type sizes, the target platform is not supported. Please, report to the developers (patches are welcome).  
```  
  
Simon

---

## Comment 1

> Username: Lastique  
> Created at: 2018-07-17 09:44:57 UTC  
> Updated at: 2018-07-17 09:45:19 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-405524329  

What target architecture are you compiling for? Can you provide the output of the following command:  
  
```  
g++ -x c++ -dM -E -  < /dev/null  
```  
  
If you're using any arch-specific or optimization options, add those after -E as well and mention in this bug report, please.

---

## Comment 2

> Username: ClundXIII  
> Created at: 2018-07-21 17:08:16 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-406810277  

```  
simon@host:~> g++ -x c++ -dM -E -  < /dev/null  
#define __DBL_MIN_EXP__ (-1021)  
#define __FLT32X_MAX_EXP__ 1024  
#define __cpp_attributes 200809  
#define __UINT_LEAST16_MAX__ 0xffff  
#define __ATOMIC_ACQUIRE 2  
#define __FLT128_MAX_10_EXP__ 4932  
#define __FLT_MIN__ 1.17549435082228750796873653722224568e-38F  
#define __GCC_IEC_559_COMPLEX 2  
#define __cpp_aggregate_nsdmi 201304  
#define __UINT_LEAST8_TYPE__ unsigned char  
#define __SIZEOF_FLOAT80__ 16  
#define __INTMAX_C(c) c ## L  
#define __CHAR_BIT__ 8  
#define __UINT8_MAX__ 0xff  
#define __WINT_MAX__ 0xffffffffU  
#define __FLT32_MIN_EXP__ (-125)  
#define __cpp_static_assert 200410  
#define __ORDER_LITTLE_ENDIAN__ 1234  
#define __SIZE_MAX__ 0xffffffffffffffffUL  
#define __WCHAR_MAX__ 0x7fffffff  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_1 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_2 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_4 1  
#define __DBL_DENORM_MIN__ double(4.94065645841246544176568792868221372e-324L)  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_8 1  
#define __GCC_ATOMIC_CHAR_LOCK_FREE 2  
#define __GCC_IEC_559 2  
#define __FLT32X_DECIMAL_DIG__ 17  
#define __FLT_EVAL_METHOD__ 0  
#define __unix__ 1  
#define __cpp_binary_literals 201304  
#define __FLT64_DECIMAL_DIG__ 17  
#define __GCC_ATOMIC_CHAR32_T_LOCK_FREE 2  
#define __x86_64 1  
#define __cpp_variadic_templates 200704  
#define __UINT_FAST64_MAX__ 0xffffffffffffffffUL  
#define __SIG_ATOMIC_TYPE__ int  
#define __DBL_MIN_10_EXP__ (-307)  
#define __FINITE_MATH_ONLY__ 0  
#define __cpp_variable_templates 201304  
#define __GNUC_PATCHLEVEL__ 1  
#define __FLT32_HAS_DENORM__ 1  
#define __UINT_FAST8_MAX__ 0xff  
#define __has_include(STR) __has_include__(STR)  
#define __DEC64_MAX_EXP__ 385  
#define __INT8_C(c) c  
#define __INT_LEAST8_WIDTH__ 8  
#define __UINT_LEAST64_MAX__ 0xffffffffffffffffUL  
#define __SHRT_MAX__ 0x7fff  
#define __LDBL_MAX__ 1.18973149535723176502126385303097021e+4932L  
#define __FLT64X_MAX_10_EXP__ 4932  
#define __UINT_LEAST8_MAX__ 0xff  
#define __GCC_ATOMIC_BOOL_LOCK_FREE 2  
#define __FLT128_DENORM_MIN__ 6.47517511943802511092443895822764655e-4966F128  
#define __UINTMAX_TYPE__ long unsigned int  
#define __linux 1  
#define __DEC32_EPSILON__ 1E-6DF  
#define __FLT_EVAL_METHOD_TS_18661_3__ 0  
#define __unix 1  
#define __UINT32_MAX__ 0xffffffffU  
#define __GXX_EXPERIMENTAL_CXX0X__ 1  
#define __LDBL_MAX_EXP__ 16384  
#define __FLT128_MIN_EXP__ (-16381)  
#define __WINT_MIN__ 0U  
#define __linux__ 1  
#define __FLT128_MIN_10_EXP__ (-4931)  
#define __INT_LEAST16_WIDTH__ 16  
#define __SCHAR_MAX__ 0x7f  
#define __FLT128_MANT_DIG__ 113  
#define __WCHAR_MIN__ (-__WCHAR_MAX__ - 1)  
#define __INT64_C(c) c ## L  
#define __DBL_DIG__ 15  
#define __GCC_ATOMIC_POINTER_LOCK_FREE 2  
#define __FLT64X_MANT_DIG__ 64  
#define __SIZEOF_INT__ 4  
#define __SIZEOF_POINTER__ 8  
#define __GCC_ATOMIC_CHAR16_T_LOCK_FREE 2  
#define __USER_LABEL_PREFIX__   
#define __FLT64X_EPSILON__ 1.08420217248550443400745280086994171e-19F64x  
#define __STDC_HOSTED__ 1  
#define __LDBL_HAS_INFINITY__ 1  
#define __FLT32_DIG__ 6  
#define __FLT_EPSILON__ 1.19209289550781250000000000000000000e-7F  
#define __GXX_WEAK__ 1  
#define __SHRT_WIDTH__ 16  
#define __LDBL_MIN__ 3.36210314311209350626267781732175260e-4932L  
#define __DEC32_MAX__ 9.999999E96DF  
#define __cpp_threadsafe_static_init 200806  
#define __FLT64X_DENORM_MIN__ 3.64519953188247460252840593361941982e-4951F64x  
#define __FLT32X_HAS_INFINITY__ 1  
#define __INT32_MAX__ 0x7fffffff  
#define __INT_WIDTH__ 32  
#define __SIZEOF_LONG__ 8  
#define __STDC_IEC_559__ 1  
#define __STDC_ISO_10646__ 201706L  
#define __UINT16_C(c) c  
#define __PTRDIFF_WIDTH__ 64  
#define __DECIMAL_DIG__ 21  
#define __FLT64_EPSILON__ 2.22044604925031308084726333618164062e-16F64  
#define __gnu_linux__ 1  
#define __INTMAX_WIDTH__ 64  
#define __FLT64_MIN_EXP__ (-1021)  
#define __has_include_next(STR) __has_include_next__(STR)  
#define __FLT64X_MIN_10_EXP__ (-4931)  
#define __LDBL_HAS_QUIET_NAN__ 1  
#define __FLT64_MANT_DIG__ 53  
#define __GNUC__ 8  
#define __GXX_RTTI 1  
#define __MMX__ 1  
#define __cpp_delegating_constructors 200604  
#define __FLT_HAS_DENORM__ 1  
#define __SIZEOF_LONG_DOUBLE__ 16  
#define __BIGGEST_ALIGNMENT__ 16  
#define __STDC_UTF_16__ 1  
#define __FLT64_MAX_10_EXP__ 308  
#define __FLT32_HAS_INFINITY__ 1  
#define __DBL_MAX__ double(1.79769313486231570814527423731704357e+308L)  
#define __cpp_raw_strings 200710  
#define __INT_FAST32_MAX__ 0x7fffffffffffffffL  
#define __DBL_HAS_INFINITY__ 1  
#define __DEC32_MIN_EXP__ (-94)  
#define __INTPTR_WIDTH__ 64  
#define __FLT32X_HAS_DENORM__ 1  
#define __INT_FAST16_TYPE__ long int  
#define __LDBL_HAS_DENORM__ 1  
#define __cplusplus 201402L  
#define __cpp_ref_qualifiers 200710  
#define __DEC128_MAX__ 9.999999999999999999999999999999999E6144DL  
#define __INT_LEAST32_MAX__ 0x7fffffff  
#define __DEC32_MIN__ 1E-95DF  
#define __DEPRECATED 1  
#define __cpp_rvalue_references 200610  
#define __DBL_MAX_EXP__ 1024  
#define __WCHAR_WIDTH__ 32  
#define __FLT32_MAX__ 3.40282346638528859811704183484516925e+38F32  
#define __DEC128_EPSILON__ 1E-33DL  
#define __SSE2_MATH__ 1  
#define __ATOMIC_HLE_RELEASE 131072  
#define __PTRDIFF_MAX__ 0x7fffffffffffffffL  
#define __amd64 1  
#define __STDC_NO_THREADS__ 1  
#define __ATOMIC_HLE_ACQUIRE 65536  
#define __FLT32_HAS_QUIET_NAN__ 1  
#define __GNUG__ 8  
#define __LONG_LONG_MAX__ 0x7fffffffffffffffLL  
#define __SIZEOF_SIZE_T__ 8  
#define __cpp_rvalue_reference 200610  
#define __cpp_nsdmi 200809  
#define __FLT64X_MIN_EXP__ (-16381)  
#define __SIZEOF_WINT_T__ 4  
#define __LONG_LONG_WIDTH__ 64  
#define __cpp_initializer_lists 200806  
#define __FLT32_MAX_EXP__ 128  
#define __cpp_hex_float 201603  
#define __GCC_HAVE_DWARF2_CFI_ASM 1  
#define __GXX_ABI_VERSION 1013  
#define __FLT128_HAS_INFINITY__ 1  
#define __FLT_MIN_EXP__ (-125)  
#define __cpp_lambdas 200907  
#define __FLT64X_HAS_QUIET_NAN__ 1  
#define __INT_FAST64_TYPE__ long int  
#define __FLT64_DENORM_MIN__ 4.94065645841246544176568792868221372e-324F64  
#define __DBL_MIN__ double(2.22507385850720138309023271733240406e-308L)  
#define __LP64__ 1  
#define __FLT32X_EPSILON__ 2.22044604925031308084726333618164062e-16F32x  
#define __DECIMAL_BID_FORMAT__ 1  
#define __FLT64_MIN_10_EXP__ (-307)  
#define __FLT64X_DECIMAL_DIG__ 21  
#define __DEC128_MIN__ 1E-6143DL  
#define __REGISTER_PREFIX__   
#define __UINT16_MAX__ 0xffff  
#define __DBL_HAS_DENORM__ 1  
#define __FLT32_MIN__ 1.17549435082228750796873653722224568e-38F32  
#define __UINT8_TYPE__ unsigned char  
#define __NO_INLINE__ 1  
#define __FLT_MANT_DIG__ 24  
#define __LDBL_DECIMAL_DIG__ 21  
#define __VERSION__ "8.1.1 20180614 [gcc-8-branch revision 261584]"  
#define __UINT64_C(c) c ## UL  
#define __cpp_unicode_characters 200704  
#define _STDC_PREDEF_H 1  
#define __cpp_decltype_auto 201304  
#define __GCC_ATOMIC_INT_LOCK_FREE 2  
#define __FLT128_MAX_EXP__ 16384  
#define __FLT32_MANT_DIG__ 24  
#define __FLOAT_WORD_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __STDC_IEC_559_COMPLEX__ 1  
#define __FLT128_HAS_DENORM__ 1  
#define __FLT128_DIG__ 33  
#define __SCHAR_WIDTH__ 8  
#define __INT32_C(c) c  
#define __DEC64_EPSILON__ 1E-15DD  
#define __ORDER_PDP_ENDIAN__ 3412  
#define __DEC128_MIN_EXP__ (-6142)  
#define __FLT32_MAX_10_EXP__ 38  
#define __INT_FAST32_TYPE__ long int  
#define __UINT_LEAST16_TYPE__ short unsigned int  
#define __FLT64X_HAS_INFINITY__ 1  
#define unix 1  
#define __INT16_MAX__ 0x7fff  
#define __cpp_rtti 199711  
#define __SIZE_TYPE__ long unsigned int  
#define __UINT64_MAX__ 0xffffffffffffffffUL  
#define __FLT64X_DIG__ 18  
#define __INT8_TYPE__ signed char  
#define __cpp_digit_separators 201309  
#define __ELF__ 1  
#define __GCC_ASM_FLAG_OUTPUTS__ 1  
#define __FLT_RADIX__ 2  
#define __INT_LEAST16_TYPE__ short int  
#define __LDBL_EPSILON__ 1.08420217248550443400745280086994171e-19L  
#define __UINTMAX_C(c) c ## UL  
#define __GLIBCXX_BITSIZE_INT_N_0 128  
#define __k8 1  
#define __SIG_ATOMIC_MAX__ 0x7fffffff  
#define __GCC_ATOMIC_WCHAR_T_LOCK_FREE 2  
#define __SIZEOF_PTRDIFF_T__ 8  
#define __FLT32X_MANT_DIG__ 53  
#define __x86_64__ 1  
#define __FLT32X_MIN_EXP__ (-1021)  
#define __DEC32_SUBNORMAL_MIN__ 0.000001E-95DF  
#define __INT_FAST16_MAX__ 0x7fffffffffffffffL  
#define __FLT64_DIG__ 15  
#define __UINT_FAST32_MAX__ 0xffffffffffffffffUL  
#define __UINT_LEAST64_TYPE__ long unsigned int  
#define __FLT_HAS_QUIET_NAN__ 1  
#define __FLT_MAX_10_EXP__ 38  
#define __LONG_MAX__ 0x7fffffffffffffffL  
#define __FLT64X_HAS_DENORM__ 1  
#define __DEC128_SUBNORMAL_MIN__ 0.000000000000000000000000000000001E-6143DL  
#define __FLT_HAS_INFINITY__ 1  
#define __cpp_unicode_literals 200710  
#define __UINT_FAST16_TYPE__ long unsigned int  
#define __DEC64_MAX__ 9.999999999999999E384DD  
#define __INT_FAST32_WIDTH__ 64  
#define __CHAR16_TYPE__ short unsigned int  
#define __PRAGMA_REDEFINE_EXTNAME 1  
#define __SIZE_WIDTH__ 64  
#define __SEG_FS 1  
#define __INT_LEAST16_MAX__ 0x7fff  
#define __DEC64_MANT_DIG__ 16  
#define __INT64_MAX__ 0x7fffffffffffffffL  
#define __UINT_LEAST32_MAX__ 0xffffffffU  
#define __SEG_GS 1  
#define __FLT32_DENORM_MIN__ 1.40129846432481707092372958328991613e-45F32  
#define __GCC_ATOMIC_LONG_LOCK_FREE 2  
#define __SIG_ATOMIC_WIDTH__ 32  
#define __INT_LEAST64_TYPE__ long int  
#define __INT16_TYPE__ short int  
#define __INT_LEAST8_TYPE__ signed char  
#define __DEC32_MAX_EXP__ 97  
#define __INT_FAST8_MAX__ 0x7f  
#define __FLT128_MAX__ 1.18973149535723176508575932662800702e+4932F128  
#define __INTPTR_MAX__ 0x7fffffffffffffffL  
#define __cpp_sized_deallocation 201309  
#define linux 1  
#define __cpp_range_based_for 200907  
#define __FLT64_HAS_QUIET_NAN__ 1  
#define __FLT32_MIN_10_EXP__ (-37)  
#define __SSE2__ 1  
#define __EXCEPTIONS 1  
#define __LDBL_MANT_DIG__ 64  
#define __DBL_HAS_QUIET_NAN__ 1  
#define __FLT64_HAS_INFINITY__ 1  
#define __FLT64X_MAX__ 1.18973149535723176502126385303097021e+4932F64x  
#define __SIG_ATOMIC_MIN__ (-__SIG_ATOMIC_MAX__ - 1)  
#define __code_model_small__ 1  
#define __cpp_return_type_deduction 201304  
#define __k8__ 1  
#define __INTPTR_TYPE__ long int  
#define __UINT16_TYPE__ short unsigned int  
#define __WCHAR_TYPE__ int  
#define __SIZEOF_FLOAT__ 4  
#define __UINTPTR_MAX__ 0xffffffffffffffffUL  
#define __INT_FAST64_WIDTH__ 64  
#define __DEC64_MIN_EXP__ (-382)  
#define __cpp_decltype 200707  
#define __FLT32_DECIMAL_DIG__ 9  
#define __INT_FAST64_MAX__ 0x7fffffffffffffffL  
#define __GCC_ATOMIC_TEST_AND_SET_TRUEVAL 1  
#define __FLT_DIG__ 6  
#define __FLT64X_MAX_EXP__ 16384  
#define __UINT_FAST64_TYPE__ long unsigned int  
#define __INT_MAX__ 0x7fffffff  
#define __amd64__ 1  
#define __INT64_TYPE__ long int  
#define __FLT_MAX_EXP__ 128  
#define __ORDER_BIG_ENDIAN__ 4321  
#define __DBL_MANT_DIG__ 53  
#define __cpp_inheriting_constructors 201511  
#define __SIZEOF_FLOAT128__ 16  
#define __INT_LEAST64_MAX__ 0x7fffffffffffffffL  
#define __DEC64_MIN__ 1E-383DD  
#define __WINT_TYPE__ unsigned int  
#define __UINT_LEAST32_TYPE__ unsigned int  
#define __SIZEOF_SHORT__ 2  
#define __SSE__ 1  
#define __LDBL_MIN_EXP__ (-16381)  
#define __FLT64_MAX__ 1.79769313486231570814527423731704357e+308F64  
#define __WINT_WIDTH__ 32  
#define __INT_LEAST8_MAX__ 0x7f  
#define __FLT32X_MAX_10_EXP__ 308  
#define __SIZEOF_INT128__ 16  
#define __LDBL_MAX_10_EXP__ 4932  
#define __ATOMIC_RELAXED 0  
#define __DBL_EPSILON__ double(2.22044604925031308084726333618164062e-16L)  
#define __FLT128_MIN__ 3.36210314311209350626267781732175260e-4932F128  
#define _LP64 1  
#define __UINT8_C(c) c  
#define __FLT64_MAX_EXP__ 1024  
#define __INT_LEAST32_TYPE__ int  
#define __SIZEOF_WCHAR_T__ 4  
#define __FLT128_HAS_QUIET_NAN__ 1  
#define __INT_FAST8_TYPE__ signed char  
#define __FLT64X_MIN__ 3.36210314311209350626267781732175260e-4932F64x  
#define __GNUC_STDC_INLINE__ 1  
#define __FLT64_HAS_DENORM__ 1  
#define __FLT32_EPSILON__ 1.19209289550781250000000000000000000e-7F32  
#define __DBL_DECIMAL_DIG__ 17  
#define __STDC_UTF_32__ 1  
#define __INT_FAST8_WIDTH__ 8  
#define __FXSR__ 1  
#define __DEC_EVAL_METHOD__ 2  
#define __FLT32X_MAX__ 1.79769313486231570814527423731704357e+308F32x  
#define __cpp_runtime_arrays 198712  
#define __UINT64_TYPE__ long unsigned int  
#define __UINT32_C(c) c ## U  
#define __INTMAX_MAX__ 0x7fffffffffffffffL  
#define __cpp_alias_templates 200704  
#define __BYTE_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __FLT_DENORM_MIN__ 1.40129846432481707092372958328991613e-45F  
#define __INT8_MAX__ 0x7f  
#define __LONG_WIDTH__ 64  
#define __UINT_FAST32_TYPE__ long unsigned int  
#define __CHAR32_TYPE__ unsigned int  
#define __FLT_MAX__ 3.40282346638528859811704183484516925e+38F  
#define __cpp_constexpr 201304  
#define __INT32_TYPE__ int  
#define __SIZEOF_DOUBLE__ 8  
#define __cpp_exceptions 199711  
#define __FLT_MIN_10_EXP__ (-37)  
#define __FLT64_MIN__ 2.22507385850720138309023271733240406e-308F64  
#define __INT_LEAST32_WIDTH__ 32  
#define __INTMAX_TYPE__ long int  
#define __DEC128_MAX_EXP__ 6145  
#define __FLT32X_HAS_QUIET_NAN__ 1  
#define __ATOMIC_CONSUME 1  
#define __GNUC_MINOR__ 1  
#define __GLIBCXX_TYPE_INT_N_0 __int128  
#define __INT_FAST16_WIDTH__ 64  
#define __UINTMAX_MAX__ 0xffffffffffffffffUL  
#define __DEC32_MANT_DIG__ 7  
#define __FLT32X_DENORM_MIN__ 4.94065645841246544176568792868221372e-324F32x  
#define __DBL_MAX_10_EXP__ 308  
#define __LDBL_DENORM_MIN__ 3.64519953188247460252840593361941982e-4951L  
#define __INT16_C(c) c  
#define __cpp_generic_lambdas 201304  
#define __STDC__ 1  
#define __FLT32X_DIG__ 15  
#define __PTRDIFF_TYPE__ long int  
#define __ATOMIC_SEQ_CST 5  
#define __UINT32_TYPE__ unsigned int  
#define __FLT32X_MIN_10_EXP__ (-307)  
#define __UINTPTR_TYPE__ long unsigned int  
#define __DEC64_SUBNORMAL_MIN__ 0.000000000000001E-383DD  
#define __DEC128_MANT_DIG__ 34  
#define __LDBL_MIN_10_EXP__ (-4931)  
#define __FLT128_EPSILON__ 1.92592994438723585305597794258492732e-34F128  
#define __SSE_MATH__ 1  
#define __SIZEOF_LONG_LONG__ 8  
#define __cpp_user_defined_literals 200809  
#define __FLT128_DECIMAL_DIG__ 36  
#define __GCC_ATOMIC_LLONG_LOCK_FREE 2  
#define __FLT32X_MIN__ 2.22507385850720138309023271733240406e-308F32x  
#define __LDBL_DIG__ 18  
#define __FLT_DECIMAL_DIG__ 9  
#define __UINT_FAST16_MAX__ 0xffffffffffffffffUL  
#define __GCC_ATOMIC_SHORT_LOCK_FREE 2  
#define __INT_LEAST64_WIDTH__ 64  
#define __UINT_FAST8_TYPE__ unsigned char  
#define _GNU_SOURCE 1  
#define __cpp_init_captures 201304  
#define __ATOMIC_ACQ_REL 4  
#define __ATOMIC_RELEASE 3  
  
```

---

## Comment 3

> Username: Lastique  
> Created at: 2019-03-10 11:34:53 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-471279875  

Sorry for the long delay. Is this still relevant?  
  
I checked the macro definitions, and Boost.Atomic should be detecting FP type sizes fine from these values. My only guess is that perhaps your `float.h` doesn't define the standard C macros from these compiler-defined macros. My local system uses gcc 8.2 at this point, and I can't reproduce the problem.

---

## Comment 4

> Username: ClundXIII  
> Created at: 2019-03-10 15:58:19 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-471318498  

There have been a few releases after my report, so it might be fixed. I am not sure if I solved this problem or if I just stayed on an older version.  
  
You can close this report if you want. I will reopen or make a new one if I ever encounter more problems :)

---

## Comment 5

> Username: Lastique  
> Created at: 2019-03-10 18:14:48 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-471329887  

Ok, thanks. To be clear, I didn't make any fixes to Boost.Atomic with regard to this report.

---

## Comment 6

> Username: rrputane  
> Created at: 2023-08-16 12:47:36 UTC  
> Updated at: 2023-08-16 14:40:52 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1680541536  

I'm getting the same issue but on FreeBSD 12.2 machine. I'm using `g++ (FreeBSD Ports Collection) 9.3.0` and boost v1.82 version. Any idea on how to fix the issue?  
  
I ran `"g++ -x c++ -dM -E -  < /dev/null"`  
  
```  
#define __DBL_MIN_EXP__ (-1021)  
#define __FLT32X_MAX_EXP__ 1024  
#define __cpp_attributes 200809  
#define __UINT_LEAST16_MAX__ 0xffff  
#define __ATOMIC_ACQUIRE 2  
#define __FLT128_MAX_10_EXP__ 4932  
#define __FLT_MIN__ 1.17549435082228750796873653722224568e-38F  
#define __GCC_IEC_559_COMPLEX 2  
#define __cpp_aggregate_nsdmi 201304  
#define __UINT_LEAST8_TYPE__ unsigned char  
#define __SIZEOF_FLOAT80__ 12  
#define __INTMAX_C(c) c ## LL  
#define __CHAR_BIT__ 8  
#define __UINT8_MAX__ 0xff  
#define __WINT_MAX__ 0x7fffffff  
#define __FLT32_MIN_EXP__ (-125)  
#define __cpp_static_assert 200410  
#define __ORDER_LITTLE_ENDIAN__ 1234  
#define __SIZE_MAX__ 0xffffffffU  
#define __WCHAR_MAX__ 0x7fffffff  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_1 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_2 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_4 1  
#define __DBL_DENORM_MIN__ double(4.94065645841246544176568792868221372e-324L)  
#define __GCC_ATOMIC_CHAR_LOCK_FREE 2  
#define __GCC_IEC_559 2  
#define __FLT32X_DECIMAL_DIG__ 17  
#define __FLT_EVAL_METHOD__ 2  
#define __unix__ 1  
#define __cpp_binary_literals 201304  
#define __FLT64_DECIMAL_DIG__ 17  
#define __GCC_ATOMIC_CHAR32_T_LOCK_FREE 2  
#define __cpp_variadic_templates 200704  
#define __UINT_FAST64_MAX__ 0xffffffffffffffffULL  
#define __SIG_ATOMIC_TYPE__ int  
#define __DBL_MIN_10_EXP__ (-307)  
#define __FINITE_MATH_ONLY__ 0  
#define __cpp_variable_templates 201304  
#define __GNUC_PATCHLEVEL__ 0  
#define __FLT32_HAS_DENORM__ 1  
#define __UINT_FAST8_MAX__ 0xffffffffU  
#define __cpp_rvalue_reference 200610  
#define __has_include(STR) __has_include__(STR)  
#define __DEC64_MAX_EXP__ 385  
#define __INT8_C(c) c  
#define __INT_LEAST8_WIDTH__ 8  
#define __UINT_LEAST64_MAX__ 0xffffffffffffffffULL  
#define __SHRT_MAX__ 0x7fff  
#define __LDBL_MAX__ 1.18973149535723163299902939989638351e+4932L  
#define __FLT64X_MAX_10_EXP__ 4932  
#define __UINT_LEAST8_MAX__ 0xff  
#define __GCC_ATOMIC_BOOL_LOCK_FREE 2  
#define __FLT128_DENORM_MIN__ 6.47517511943802511092443895822764655e-4966F128  
#define __UINTMAX_TYPE__ long long unsigned int  
#define __DEC32_EPSILON__ 1E-6DF  
#define __FLT_EVAL_METHOD_TS_18661_3__ 2  
#define __unix 1  
#define __UINT32_MAX__ 0xffffffffU  
#define __GXX_EXPERIMENTAL_CXX0X__ 1  
#define __LDBL_MAX_EXP__ 16384  
#define __FLT128_MIN_EXP__ (-16381)  
#define __WINT_MIN__ (-__WINT_MAX__ - 1)  
#define __FLT128_MIN_10_EXP__ (-4931)  
#define __INT_LEAST16_WIDTH__ 16  
#define __SCHAR_MAX__ 0x7f  
#define __FLT128_MANT_DIG__ 113  
#define __WCHAR_MIN__ (-__WCHAR_MAX__ - 1)  
#define __INT64_C(c) c ## LL  
#define __DBL_DIG__ 15  
#define __GCC_ATOMIC_POINTER_LOCK_FREE 2  
#define __FLT64X_MANT_DIG__ 113  
#define __SIZEOF_INT__ 4  
#define __SIZEOF_POINTER__ 4  
#define __GCC_ATOMIC_CHAR16_T_LOCK_FREE 2  
#define __USER_LABEL_PREFIX__  
#define __FLT64X_EPSILON__ 1.92592994438723585305597794258492732e-34F64x  
#define __STDC_HOSTED__ 1  
#define __LDBL_HAS_INFINITY__ 1  
#define __FLT32_DIG__ 6  
#define __FLT_EPSILON__ 1.19209289550781250000000000000000000e-7F  
#define __GXX_WEAK__ 1  
#define __SHRT_WIDTH__ 16  
#define __LDBL_MIN__ 3.36210314311209350626267781732175260e-4932L  
#define __DEC32_MAX__ 9.999999E96DF  
#define __cpp_threadsafe_static_init 200806  
#define __FLT64X_DENORM_MIN__ 6.47517511943802511092443895822764655e-4966F64x  
#define __FLT32X_HAS_INFINITY__ 1  
#define __INT32_MAX__ 0x7fffffff  
#define __INT_WIDTH__ 32  
#define __SIZEOF_LONG__ 4  
#define __UINT16_C(c) c  
#define __PTRDIFF_WIDTH__ 32  
#define __DECIMAL_DIG__ 17  
#define __FLT64_EPSILON__ 2.22044604925031308084726333618164062e-16F64  
#define __INTMAX_WIDTH__ 64  
#define __FLT64_MIN_EXP__ (-1021)  
#define __has_include_next(STR) __has_include_next__(STR)  
#define __FLT64X_MIN_10_EXP__ (-4931)  
#define __LDBL_HAS_QUIET_NAN__ 1  
#define __FLT64_MANT_DIG__ 53  
#define __GNUC__ 9  
#define __GXX_RTTI 1  
#define __cpp_delegating_constructors 200604  
#define __FLT_HAS_DENORM__ 1  
#define __SIZEOF_LONG_DOUBLE__ 12  
#define __BIGGEST_ALIGNMENT__ 16  
#define __STDC_UTF_16__ 1  
#define __FLT64_MAX_10_EXP__ 308  
#define __FLT32_HAS_INFINITY__ 1  
#define __DBL_MAX__ double(1.79769313486231570814527423731704357e+308L)  
#define __cpp_raw_strings 200710  
#define __INT_FAST32_MAX__ 0x7fffffff  
#define __DBL_HAS_INFINITY__ 1  
#define __HAVE_SPECULATION_SAFE_VALUE 1  
#define __DEC32_MIN_EXP__ (-94)  
#define __INTPTR_WIDTH__ 32  
#define __FLT32X_HAS_DENORM__ 1  
#define __INT_FAST16_TYPE__ int  
#define __LDBL_HAS_DENORM__ 1  
#define __cplusplus 201402L  
#define __cpp_ref_qualifiers 200710  
#define __DEC128_MAX__ 9.999999999999999999999999999999999E6144DL  
#define __INT_LEAST32_MAX__ 0x7fffffff  
#define __DEC32_MIN__ 1E-95DF  
#define __DEPRECATED 1  
#define __cpp_rvalue_references 200610  
#define __DBL_MAX_EXP__ 1024  
#define __WCHAR_WIDTH__ 32  
#define __FLT32_MAX__ 3.40282346638528859811704183484516925e+38F32  
#define __DEC128_EPSILON__ 1E-33DL  
#define __ATOMIC_HLE_RELEASE 131072  
#define __PTRDIFF_MAX__ 0x7fffffff  
#define __ATOMIC_HLE_ACQUIRE 65536  
#define __FLT32_HAS_QUIET_NAN__ 1  
#define __GNUG__ 9  
#define __LONG_LONG_MAX__ 0x7fffffffffffffffLL  
#define __SIZEOF_SIZE_T__ 4  
#define __cpp_nsdmi 200809  
#define __FLT64X_MIN_EXP__ (-16381)  
#define __SIZEOF_WINT_T__ 4  
#define __LONG_LONG_WIDTH__ 64  
#define __cpp_initializer_lists 200806  
#define __FLT32_MAX_EXP__ 128  
#define __cpp_hex_float 201603  
#define __GCC_HAVE_DWARF2_CFI_ASM 1  
#define __GXX_ABI_VERSION 1013  
#define __FLT128_HAS_INFINITY__ 1  
#define __FLT_MIN_EXP__ (-125)  
#define __cpp_lambdas 200907  
#define __FLT64X_HAS_QUIET_NAN__ 1  
#define __INT_FAST64_TYPE__ long long int  
#define __FLT64_DENORM_MIN__ 4.94065645841246544176568792868221372e-324F64  
#define __DBL_MIN__ double(2.22507385850720138309023271733240406e-308L)  
#define __FLT32X_EPSILON__ 2.22044604925031308084726333618164062e-16F32x  
#define __FLT64_MIN_10_EXP__ (-307)  
#define __FLT64X_DECIMAL_DIG__ 36  
#define __DEC128_MIN__ 1E-6143DL  
#define __REGISTER_PREFIX__  
#define __UINT16_MAX__ 0xffff  
#define __FLT32_MIN__ 1.17549435082228750796873653722224568e-38F32  
#define __UINT8_TYPE__ unsigned char  
#define __NO_INLINE__ 1  
#define __i386 1  
#define __FLT_MANT_DIG__ 24  
#define __LDBL_DECIMAL_DIG__ 17  
#define __VERSION__ "9.3.0"  
#define __UINT64_C(c) c ## ULL  
#define __cpp_unicode_characters 200704  
#define __cpp_decltype_auto 201304  
#define __GCC_ATOMIC_INT_LOCK_FREE 2  
#define __FLT128_MAX_EXP__ 16384  
#define __FLT32_MANT_DIG__ 24  
#define __FLOAT_WORD_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __FLT128_HAS_DENORM__ 1  
#define __FLT128_DIG__ 33  
#define __SCHAR_WIDTH__ 8  
#define __INT32_C(c) c  
#define __DEC64_EPSILON__ 1E-15DD  
#define __ORDER_PDP_ENDIAN__ 3412  
#define __DEC128_MIN_EXP__ (-6142)  
#define __code_model_32__ 1  
#define __FLT32_MAX_10_EXP__ 38  
#define __INT_FAST32_TYPE__ int  
#define __i486__ 1  
#define __UINT_LEAST16_TYPE__ short unsigned int  
#define __FLT64X_HAS_INFINITY__ 1  
#define unix 1  
#define __DBL_HAS_DENORM__ 1  
#define __INT16_MAX__ 0x7fff  
#define __i386__ 1  
#define __cpp_rtti 199711  
#define __SIZE_TYPE__ unsigned int  
#define __UINT64_MAX__ 0xffffffffffffffffULL  
#define __FLT64X_DIG__ 33  
#define __INT8_TYPE__ signed char  
#define __cpp_digit_separators 201309  
#define __ELF__ 1  
#define __GCC_ASM_FLAG_OUTPUTS__ 1  
#define __FLT_RADIX__ 2  
#define __INT_LEAST16_TYPE__ short int  
#define __LDBL_EPSILON__ 2.22044604925031308084726333618164062e-16L  
#define __UINTMAX_C(c) c ## ULL  
#define __FreeBSD__ 12  
#define __SIG_ATOMIC_MAX__ 0x7fffffff  
#define __GCC_ATOMIC_WCHAR_T_LOCK_FREE 2  
#define __SIZEOF_PTRDIFF_T__ 4  
#define __FLT32X_MANT_DIG__ 53  
#define __KPRINTF_ATTRIBUTE__ 1  
#define __FLT32X_MIN_EXP__ (-1021)  
#define __DEC32_SUBNORMAL_MIN__ 0.000001E-95DF  
#define __INT_FAST16_MAX__ 0x7fffffff  
#define __FLT64_DIG__ 15  
#define __UINT_FAST32_MAX__ 0xffffffffU  
#define __UINT_LEAST64_TYPE__ long long unsigned int  
#define __FLT_HAS_QUIET_NAN__ 1  
#define __FLT_MAX_10_EXP__ 38  
#define __LONG_MAX__ 0x7fffffffL  
#define __FLT64X_HAS_DENORM__ 1  
#define __DEC128_SUBNORMAL_MIN__ 0.000000000000000000000000000000001E-6143DL  
#define __FLT_HAS_INFINITY__ 1  
#define __cpp_unicode_literals 200710  
#define __UINT_FAST16_TYPE__ unsigned int  
#define __DEC64_MAX__ 9.999999999999999E384DD  
#define __INT_FAST32_WIDTH__ 32  
#define __CHAR16_TYPE__ short unsigned int  
#define __PRAGMA_REDEFINE_EXTNAME 1  
#define __SIZE_WIDTH__ 32  
#define __SEG_FS 1  
#define __INT_LEAST16_MAX__ 0x7fff  
#define __DEC64_MANT_DIG__ 16  
#define __INT64_MAX__ 0x7fffffffffffffffLL  
#define __UINT_LEAST32_MAX__ 0xffffffffU  
#define __SEG_GS 1  
#define __FLT32_DENORM_MIN__ 1.40129846432481707092372958328991613e-45F32  
#define __GCC_ATOMIC_LONG_LOCK_FREE 2  
#define __SIG_ATOMIC_WIDTH__ 32  
#define __INT_LEAST64_TYPE__ long long int  
#define __INT16_TYPE__ short int  
#define __INT_LEAST8_TYPE__ signed char  
#define __DEC32_MAX_EXP__ 97  
#define __INT_FAST8_MAX__ 0x7fffffff  
#define __FLT128_MAX__ 1.18973149535723176508575932662800702e+4932F128  
#define __INTPTR_MAX__ 0x7fffffff  
#define __cpp_sized_deallocation 201309  
#define __cpp_range_based_for 200907  
#define __FLT64_HAS_QUIET_NAN__ 1  
#define __FLT32_MIN_10_EXP__ (-37)  
#define __EXCEPTIONS 1  
#define __LDBL_MANT_DIG__ 53  
#define __DBL_HAS_QUIET_NAN__ 1  
#define __FLT64_HAS_INFINITY__ 1  
#define __FLT64X_MAX__ 1.18973149535723176508575932662800702e+4932F64x  
#define __SIG_ATOMIC_MIN__ (-__SIG_ATOMIC_MAX__ - 1)  
#define __cpp_return_type_deduction 201304  
#define __INTPTR_TYPE__ int  
#define __UINT16_TYPE__ short unsigned int  
#define __WCHAR_TYPE__ int  
#define __SIZEOF_FLOAT__ 4  
#define __UINTPTR_MAX__ 0xffffffffU  
#define __INT_FAST64_WIDTH__ 64  
#define __DEC64_MIN_EXP__ (-382)  
#define __cpp_decltype 200707  
#define __FLT32_DECIMAL_DIG__ 9  
#define __INT_FAST64_MAX__ 0x7fffffffffffffffLL  
#define __GCC_ATOMIC_TEST_AND_SET_TRUEVAL 1  
#define __FLT_DIG__ 6  
#define __FLT64X_MAX_EXP__ 16384  
#define __UINT_FAST64_TYPE__ long long unsigned int  
#define __INT_MAX__ 0x7fffffff  
#define __i486 1  
#define __INT64_TYPE__ long long int  
#define __FLT_MAX_EXP__ 128  
#define __DBL_MANT_DIG__ 53  
#define __cpp_inheriting_constructors 201511  
#define __SIZEOF_FLOAT128__ 16  
#define __INT_LEAST64_MAX__ 0x7fffffffffffffffLL  
#define __DEC64_MIN__ 1E-383DD  
#define __WINT_TYPE__ int  
#define __UINT_LEAST32_TYPE__ unsigned int  
#define __SIZEOF_SHORT__ 2  
#define __LDBL_MIN_EXP__ (-16381)  
#define __FLT64_MAX__ 1.79769313486231570814527423731704357e+308F64  
#define __WINT_WIDTH__ 32  
#define __INT_LEAST8_MAX__ 0x7f  
#define __FLT32X_MAX_10_EXP__ 308  
#define __LDBL_MAX_10_EXP__ 4932  
#define __ATOMIC_RELAXED 0  
#define __DBL_EPSILON__ double(2.22044604925031308084726333618164062e-16L)  
#define __FLT128_MIN__ 3.36210314311209350626267781732175260e-4932F128  
#define __UINT8_C(c) c  
#define __FLT64_MAX_EXP__ 1024  
#define __INT_LEAST32_TYPE__ int  
#define __SIZEOF_WCHAR_T__ 4  
#define __FLT128_HAS_QUIET_NAN__ 1  
#define __INT_FAST8_TYPE__ int  
#define __FLT64X_MIN__ 3.36210314311209350626267781732175260e-4932F64x  
#define __GNUC_STDC_INLINE__ 1  
#define __FLT64_HAS_DENORM__ 1  
#define __FLT32_EPSILON__ 1.19209289550781250000000000000000000e-7F32  
#define __DBL_DECIMAL_DIG__ 17  
#define __STDC_UTF_32__ 1  
#define __INT_FAST8_WIDTH__ 32  
#define __DEC_EVAL_METHOD__ 2  
#define __FLT32X_MAX__ 1.79769313486231570814527423731704357e+308F32x  
#define __ORDER_BIG_ENDIAN__ 4321  
#define __cpp_runtime_arrays 198712  
#define __UINT64_TYPE__ long long unsigned int  
#define __UINT32_C(c) c ## U  
#define __INTMAX_MAX__ 0x7fffffffffffffffLL  
#define __cpp_alias_templates 200704  
#define __BYTE_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __FLT_DENORM_MIN__ 1.40129846432481707092372958328991613e-45F  
#define __INT8_MAX__ 0x7f  
#define __LONG_WIDTH__ 32  
#define __UINT_FAST32_TYPE__ unsigned int  
#define __CHAR32_TYPE__ unsigned int  
#define __FLT_MAX__ 3.40282346638528859811704183484516925e+38F  
#define __cpp_constexpr 201304  
#define __INT32_TYPE__ int  
#define __SIZEOF_DOUBLE__ 8  
#define __cpp_exceptions 199711  
#define __FLT_MIN_10_EXP__ (-37)  
#define __FLT64_MIN__ 2.22507385850720138309023271733240406e-308F64  
#define __INT_LEAST32_WIDTH__ 32  
#define __INTMAX_TYPE__ long long int  
#define i386 1  
#define __DEC128_MAX_EXP__ 6145  
#define __FLT32X_HAS_QUIET_NAN__ 1  
#define __ATOMIC_CONSUME 1  
#define __GNUC_MINOR__ 3  
#define __INT_FAST16_WIDTH__ 32  
#define __UINTMAX_MAX__ 0xffffffffffffffffULL  
#define __DEC32_MANT_DIG__ 7  
#define __FLT32X_DENORM_MIN__ 4.94065645841246544176568792868221372e-324F32x  
#define __DBL_MAX_10_EXP__ 308  
#define __LDBL_DENORM_MIN__ 7.46536864129530798597817535205257178e-4948L  
#define __INT16_C(c) c  
#define __cpp_generic_lambdas 201304  
#define __STDC__ 1  
#define __FLT32X_DIG__ 15  
#define __PTRDIFF_TYPE__ int  
#define __ATOMIC_SEQ_CST 5  
#define __UINT32_TYPE__ unsigned int  
#define __FLT32X_MIN_10_EXP__ (-307)  
#define __UINTPTR_TYPE__ unsigned int  
#define __DEC64_SUBNORMAL_MIN__ 0.000000000000001E-383DD  
#define __DEC128_MANT_DIG__ 34  
#define __LDBL_MIN_10_EXP__ (-4931)  
#define __FLT128_EPSILON__ 1.92592994438723585305597794258492732e-34F128  
#define __SIZEOF_LONG_LONG__ 8  
#define __cpp_user_defined_literals 200809  
#define __FLT128_DECIMAL_DIG__ 36  
#define __GCC_ATOMIC_LLONG_LOCK_FREE 1  
#define __FLT32X_MIN__ 2.22507385850720138309023271733240406e-308F32x  
#define __LDBL_DIG__ 15  
#define __FLT_DECIMAL_DIG__ 9  
#define __UINT_FAST16_MAX__ 0xffffffffU  
#define __GCC_ATOMIC_SHORT_LOCK_FREE 2  
#define __INT_LEAST64_WIDTH__ 64  
#define __UINT_FAST8_TYPE__ unsigned int  
#define __cpp_init_captures 201304  
#define __ATOMIC_ACQ_REL 4  
#define __ATOMIC_RELEASE 3  
```

---

## Comment 7

> Username: Lastique  
> Created at: 2023-08-16 15:09:48 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1680797127  

Floating point value sizes are deduced from the standard [macros](https://en.cppreference.com/w/c/types/limits) defined in `float.h`, specifically `FLT_RADIX`, `FLT_MANT_DIG`, `FLT_MAX_EXP`, as well as the `DBL_*` and `LDBL_*` counterparts. Can you post the values of these macros on your system?

---

## Comment 8

> Username: Lastique  
> Created at: 2023-08-16 15:13:54 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1680803799  

Also, while testing, make sure that you are using the same include paths as your code that fails to compile. It is possible that you have multiple `float.h` headers, some of which not defining the standard macros.

---

## Comment 9

> Username: rrputane  
> Created at: 2023-08-17 05:18:04 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1681628982  

```  
FLT_RADIX: 2  
FLT_MANT_DIG: 24  
FLT_MAX_EXP: 128  
DBL_MANT_DIG: 53  
DBL_MAX_EXP: 1024  
LDBL_MANT_DIG: 53  
LDBL_MAX_EXP: 16384  
```  
  
And yes, there are multiple `float.h` headers present.  
  
```  
/usr/include/c++/v1/float.h  
/usr/include/c++/v1/tr1/float.h  
/usr/include/float.h  
/usr/include/machine/float.h  
/usr/include/x86/float.h  
/usr/local/lib/gcc9/gcc/i386-portbld-freebsd12.2/9.3.0/include/float.h  
/usr/local/lib/gcc9/include/c++/tr1/float.h  
```

---

## Comment 10

> Username: rrputane  
> Created at: 2023-08-17 07:13:05 UTC  
> Updated at: 2023-08-17 10:52:58 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1681752412  

The issue is seen when `#include <boost/thread.hpp>` is included.   
  
I've written a small code snippet which prints the macro values. If I don't add `#include <boost/thread.hpp>`, it works fine. But if it is added, it gives "**_In file included from retk/dev/src/boost/boost/atomic/detail/capabilities.hpp:21,  
                 from retk/dev/src/boost/boost/atomic/capabilities.hpp:18,  
                 from retk/dev/src/boost/boost/atomic.hpp:14,  
                 from retk/dev/src/boost/boost/thread/pthread/once_atomic.hpp:20,  
                 from retk/dev/src/boost/boost/thread/once.hpp:26,  
                 from retk/dev/src/boost/boost/thread.hpp:17,  
                 from float_value.cpp:4:  
retk/dev/src/boost/boost/atomic/detail/float_sizes.hpp:139:2: error: #error Boost.Atomic: Failed to determine builtin floating point type sizes, the target platform is not supported. Please, report to the developers (patches are welcome)_**" error. I'm not including any directories in particular. Build command : `g++ float_value.cpp -Iboost_1_82_0/boost`  
  
```  
#include <iostream>  
#include <float.h>  
  
#include <boost/thread.hpp>  
  
int main() {  
    std::cout << "FLT_RADIX: " << FLT_RADIX << std::endl;  
    std::cout << "FLT_MANT_DIG: " << FLT_MANT_DIG << std::endl;  
    std::cout << "FLT_MAX_EXP: " << FLT_MAX_EXP << std::endl;  
    std::cout << "DBL_MANT_DIG: " << DBL_MANT_DIG << std::endl;  
    std::cout << "DBL_MAX_EXP: " << DBL_MAX_EXP << std::endl;  
    std::cout << "LDBL_MANT_DIG: " << LDBL_MANT_DIG << std::endl;  
    std::cout << "LDBL_MAX_EXP: " << LDBL_MAX_EXP << std::endl;  
  
    return 0;  
}  
```  
  
In both the cases, it referring to `/usr/local/lib/gcc9/gcc/i386-portbld-freebsd12.2/9.3.0/include/float.h` file.

---

## Comment 11

> Username: Lastique  
> Created at: 2023-08-17 22:10:01 UTC  
> Updated at: 2023-08-17 22:14:45 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1683042608  

Looks like the compiler is somehow unexpectedly (incorrectly?) configured:  
  
```  
LDBL_MANT_DIG: 53  
LDBL_MAX_EXP: 16384  
```  
  
For x86 80-bit [extended precision](https://en.wikipedia.org/wiki/Extended_precision?useskin=vector#x86_extended_precision_format) format, `LDBL_MANT_DIG` should be equal to 64, not 53. OTOH, if `long double` was the same as `double`, i.e. [64-bit floating point number](https://en.wikipedia.org/wiki/Double-precision_floating-point_format), then `LDBL_MAX_EXP` would have been 1024. But with values like you posted, this is some unknown floating point format midway between the two.  
  
Do you know what is the binary representation of the `long double` type on your platform? Specifically, I need to know which bits of the 12 bytes of a `long double` represent the value and which bits are padding.  
  
Did you configure the compiler in some special way when building the compiler? Do you pass any compiler options that could influence the format of floating point types?

---

## Comment 12

> Username: rrputane  
> Created at: 2023-08-21 07:53:10 UTC  
> Updated at: 2023-08-21 11:06:15 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1685830765  

No, I didn't pass any compiler options. I tried to print the binary representation of `long double` type. Output is `Binary representation of long double: 00000000 00000000 01000000 00000000 11001001 00001111 11011010 10100010 00100001 01101000 11000000 00000000`.   
  
But isn't this as expected?   
  
In the IEEE 754 format:  
The exponent bias for single precision (32-bit) is 127.  
The exponent bias for double precision (64-bit) is 1023.  
The exponent bias for double-extended precision (80-bit) is 16383.  
  
I'm using the pre-installed compilers available on the FreeBSD x86 machines. I tried with different compilers. Almost everything is printing LDBL_MANT_DIG as 53. But only GCC 4.2.1 prints 64. So it looks like GCC > 4.2.1 prints it as 53.  
  
In all FreeBSD x64 platforms, LDBL_MANT_DIG is 64.  
  
According to this article, https://learn.microsoft.com/en-us/cpp/c-language/limits-on-floating-point-constants?view=msvc-170, value of LDBL_MANT_DIG is 53.

---

## Comment 13

> Username: rrputane  
> Created at: 2023-08-21 13:19:00 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1686316180  

Also, is there any doc which tells the compatible gcc versions/platforms with different boost versions? I went through https://www.boost.org/users/history/   
It tells about the test compilers but not the support. Is it the same?

---

## Comment 14

> Username: Lastique  
> Created at: 2023-08-21 16:33:38 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1686655794  

The easiest way to analyze binary representation is to print out representation of zero, `std::numeric_limits< long double >::lowest()`, `-std::numeric_limits< long double >::infinity()` and `std::numeric_limits< long double >::infinity()`:  
  
- zero will have all value bits set to 0;  
- `lowest` will be a negative value (i.e. the sign bit is 1) with all mantissa bits set and exponent being one less than max;  
- `-infinity` will have the sign bit and all exponent bits set to 1, but all mantissa bits 0;  
- `infinity` will have the same representation, but with sign bit set to 0.  
  
Any other bits (i.e. padding bits) will contain garbage (including, but not necessarily zeros) and may change from one run to another or by modifying the surrounding unrelated code.  
  
Here is an example on [Godbolt](https://godbolt.org/z/o5boTGace):  
  
```  
#include <iostream>  
#include <iomanip>  
#include <limits>  
#include <cstring>  
#include <float.h>  
  
void print_representation(long double num)  
{  
    unsigned char buf[sizeof(num)] = {};  
    std::memcpy(buf, &num, sizeof(num));  
  
    for (unsigned int i = 0u; i < sizeof(buf); ++i)  
    {  
        if ((i % 4u) == 0u)  
            std::cout << ' ';  
  
        std::cout << std::hex << std::setw(2) << std::setfill('0')  
            << static_cast< unsigned int >(buf[i]);  
    }  
  
    std::cout << std::endl;  
}  
  
int main()  
{  
    std::cout << "LDBL_MANT_DIG: " << LDBL_MANT_DIG  
        << "\nLDBL_MAX_EXP: " << LDBL_MAX_EXP  
        << std::endl;  
  
    print_representation(static_cast< long double >(0.0));  
    print_representation(std::numeric_limits< long double >::lowest());  
    print_representation(-std::numeric_limits< long double >::infinity());  
    print_representation(std::numeric_limits< long double >::infinity());  
}  
```  
  
```  
LDBL_MANT_DIG: 64  
LDBL_MAX_EXP: 16384  
 00000000 00000000 00000000  
 ffffffff ffffffff feff0000  
 00000000 00000080 ffff0000  
 00000000 00000080 ff7f0000  
```  
  
You can see that the mantissa is 63 bits long (and one bit is an implicit 1, hence `LDBL_MANT_DIG` is 64). Luckily, all padding bits turned out zero in this example, so we can clearly see the value representation is in the first 10 bytes. This kind of output I see on Linux, for 32 and 64-bit x86 target on various gcc versions.  
  
What does this code output on your system?  
  
> According to this article, https://learn.microsoft.com/en-us/cpp/c-language/limits-on-floating-point-constants?view=msvc-170, value of LDBL_MANT_DIG is 53.  
  
MSVC does not support 80-bit `long double`, its `long double` is equivalent to `double`, i.e. IEEE 754 binary64.  
  
> Also, is there any doc which tells the compatible gcc versions/platforms with different boost versions?  
  
There's [this page](https://www.boost.org/doc/libs/1_83_0/libs/atomic/doc/html/atomic/porting.html#atomic.porting.tested_compilers), but it's not being updated regularly. You can see the list of tested configurations in the CI configs [here](https://github.com/boostorg/atomic/blob/001c1d96a80524e1c2d10b2ad3b8fedb0620fa05/.github/workflows/ci.yml) and [here](https://github.com/boostorg/atomic/blob/001c1d96a80524e1c2d10b2ad3b8fedb0620fa05/appveyor.yml). We don't test FreeBSD as there's no free CI that offers it.

---

## Comment 15

> Username: rrputane  
> Created at: 2023-08-22 07:14:57 UTC  
> Updated at: 2023-08-22 07:18:15 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1687607844  

The output for the above program on FreeBSD 10.2 x86 machine with g++ 6.0.0 is   
  
```  
LDBL_MANT_DIG: 53  
LDBL_MAX_EXP: 16384  
 00000000 00000000 00000000  
 00f8ffff ffffffff feffbfbf  
 00000000 00000080 ffffbfbf  
 00000000 00000080 ff7fbfbf  
```  
   
 It was not getting executed by default g++. It was of C++98 standard by default. So I used -std:c++11 to execute the above program. `g++ binary_rep.cpp -std=c++98`   
  
I'm seeing the same output on FreeBSD 12.2 x86 machine with g++ 9.3.0 (std:c++14) as well.

---

## Comment 16

> Username: Lastique  
> Created at: 2023-08-22 09:45:13 UTC  
> Url: https://github.com/boostorg/atomic/issues/14#issuecomment-1687847015  

Ok, I think I understand. Looks like on FreeBSD x87 is configured to round the 64-bit mantissa to 53 bits by default, and the compiler indicates this via `LDBL_MANT_DIG` being 53. The value is still 80 bits long, but the lowest 11 bits of mantissa are zero. I have found [this article](https://en.wikipedia.org/wiki/Long_double#Implementations) that also mentions OpenBSD doing this.  
  
Thanks for testing, I will update the value size detection accordingly.
