# #42 - Build failure on nios2 architecture [Closed]

> Username: ffontaine  
> Created at: 2020-11-25 22:36:32 UTC  
> Updated at: 2020-11-26 08:04:34 UTC  
> Closed at: 2020-11-26 08:04:33 UTC  
> Url: https://github.com/boostorg/atomic/issues/42  

Since version 1.74.0, boost fails to build on nios2:  
  
```  
In file included from libs/atomic/src/lock_pool.cpp:29:  
libs/atomic/src/lock_pool.cpp:1029:25: error: static assertion failed: Boost.Atomic unsupported target platform: native atomic operations not implemented for bytes  
 BOOST_STATIC_ASSERT_MSG(once_flag_operations::is_always_lock_free, "Boost.Atomic unsupported target platform: native atomic operations not implemented for bytes");  
                         ^~~~~~~~~~~~~~~~~~~~  
```  
  
Full build log is available here:  
http://autobuild.buildroot.org/results/c03/c03a786791e3aa7801cf1bff9934c4a105f54ce1/build-end.log

---

## Comment 1

> Username: Lastique  
> Created at: 2020-11-26 06:50:06 UTC  
> Url: https://github.com/boostorg/atomic/issues/42#issuecomment-734109856  

If the build fails then that platform is indeed unsupported. I have no knowledge about nios2, nor have I access to this platform, so I cannot add support for it. PRs are welcome.  
  
Can you provide the output of the `g++ -x c++ -dM -E -  < /dev/null` command on that platform? Add any target-specific options, if needed.

---

## Comment 2

> Username: ffontaine  
> Created at: 2020-11-26 07:06:42 UTC  
> Url: https://github.com/boostorg/atomic/issues/42#issuecomment-734116720  

It was building fine until version 1.74, I assume that the issue could be linked to the changes made with https://github.com/boostorg/atomic/commit/76e25f36a389609c16fa4c1d761426a17bfd5358.  
  
Here is the output of `output/host/opt/ext-toolchain/bin/nios2-buildroot-linux-gnu-g++ -x c++ -dM -E - < /dev/null`:  
```  
#define __DBL_MIN_EXP__ (-1021)  
#define __FLT32X_MAX_EXP__ 1024  
#define __cpp_attributes 200809  
#define __UINT_LEAST16_MAX__ 0xffff  
#define __ATOMIC_ACQUIRE 2  
#define __FLT_MIN__ 1.1754943508222875e-38F  
#define __GCC_IEC_559_COMPLEX 0  
#define __cpp_aggregate_nsdmi 201304  
#define __UINT_LEAST8_TYPE__ unsigned char  
#define __INTMAX_C(c) c ## LL  
#define __CHAR_BIT__ 8  
#define __UINT8_MAX__ 0xff  
#define __WINT_MAX__ 0xffffffffU  
#define __FLT32_MIN_EXP__ (-125)  
#define __cpp_static_assert 200410  
#define nios2_little_endian 1  
#define __ORDER_LITTLE_ENDIAN__ 1234  
#define __SIZE_MAX__ 0xffffffffU  
#define __WCHAR_MAX__ 0x7fffffff  
#define __DBL_DENORM_MIN__ double(4.9406564584124654e-324L)  
#define __GCC_ATOMIC_CHAR_LOCK_FREE 1  
#define __GCC_IEC_559 0  
#define __nios2_arch__ 1  
#define __FLT32X_DECIMAL_DIG__ 17  
#define __FLT_EVAL_METHOD__ 0  
#define __unix__ 1  
#define __cpp_binary_literals 201304  
#define __FLT64_DECIMAL_DIG__ 17  
#define __GCC_ATOMIC_CHAR32_T_LOCK_FREE 1  
#define __nios2_little_endian__ 1  
#define __cpp_variadic_templates 200704  
#define __UINT_FAST64_MAX__ 0xffffffffffffffffULL  
#define __SIG_ATOMIC_TYPE__ int  
#define __DBL_MIN_10_EXP__ (-307)  
#define __FINITE_MATH_ONLY__ 0  
#define __cpp_variable_templates 201304  
#define __GNUC_PATCHLEVEL__ 0  
#define __FLT32_HAS_DENORM__ 1  
#define __UINT_FAST8_MAX__ 0xff  
#define __has_include(STR) __has_include__(STR)  
#define __DEC64_MAX_EXP__ 385  
#define __INT8_C(c) c  
#define __INT_LEAST8_WIDTH__ 8  
#define __UINT_LEAST64_MAX__ 0xffffffffffffffffULL  
#define __SHRT_MAX__ 0x7fff  
#define __LDBL_MAX__ 1.7976931348623157e+308L  
#define __UINT_LEAST8_MAX__ 0xff  
#define __GCC_ATOMIC_BOOL_LOCK_FREE 1  
#define __UINTMAX_TYPE__ long long unsigned int  
#define __linux 1  
#define __DEC32_EPSILON__ 1E-6DF  
#define __FLT_EVAL_METHOD_TS_18661_3__ 0  
#define __unix 1  
#define __UINT32_MAX__ 0xffffffffU  
#define __GXX_EXPERIMENTAL_CXX0X__ 1  
#define __LDBL_MAX_EXP__ 1024  
#define __WINT_MIN__ 0U  
#define __linux__ 1  
#define __INT_LEAST16_WIDTH__ 16  
#define __SCHAR_MAX__ 0x7f  
#define __WCHAR_MIN__ (-__WCHAR_MAX__ - 1)  
#define __INT64_C(c) c ## LL  
#define __nios2__ 1  
#define __DBL_DIG__ 15  
#define __GCC_ATOMIC_POINTER_LOCK_FREE 1  
#define __SIZEOF_INT__ 4  
#define __SIZEOF_POINTER__ 4  
#define __GCC_ATOMIC_CHAR16_T_LOCK_FREE 1  
#define __USER_LABEL_PREFIX__   
#define __STDC_HOSTED__ 1  
#define __LDBL_HAS_INFINITY__ 1  
#define __FLT32_DIG__ 6  
#define __FLT_EPSILON__ 1.1920928955078125e-7F  
#define __GXX_WEAK__ 1  
#define __SHRT_WIDTH__ 16  
#define __LDBL_MIN__ 2.2250738585072014e-308L  
#define __DEC32_MAX__ 9.999999E96DF  
#define __cpp_threadsafe_static_init 200806  
#define __FLT32X_HAS_INFINITY__ 1  
#define __INT32_MAX__ 0x7fffffff  
#define __INT_WIDTH__ 32  
#define __SIZEOF_LONG__ 4  
#define __nios2 1  
#define __STDC_ISO_10646__ 201706L  
#define __UINT16_C(c) c  
#define __PTRDIFF_WIDTH__ 32  
#define __DECIMAL_DIG__ 17  
#define __FLT64_EPSILON__ 2.2204460492503131e-16F64  
#define __gnu_linux__ 1  
#define __INTMAX_WIDTH__ 64  
#define __FLT64_MIN_EXP__ (-1021)  
#define __has_include_next(STR) __has_include_next__(STR)  
#define __NIOS2 1  
#define __LDBL_HAS_QUIET_NAN__ 1  
#define __FLT64_MANT_DIG__ 53  
#define __GNUC__ 8  
#define __GXX_RTTI 1  
#define __cpp_delegating_constructors 200604  
#define __FLT_HAS_DENORM__ 1  
#define __SIZEOF_LONG_DOUBLE__ 8  
#define __BIGGEST_ALIGNMENT__ 4  
#define __STDC_UTF_16__ 1  
#define __FLT64_MAX_10_EXP__ 308  
#define __NIOS2__ 1  
#define __FLT32_HAS_INFINITY__ 1  
#define __DBL_MAX__ double(1.7976931348623157e+308L)  
#define __cpp_raw_strings 200710  
#define __INT_FAST32_MAX__ 0x7fffffff  
#define __DBL_HAS_INFINITY__ 1  
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
#define __FLT32_MAX__ 3.4028234663852886e+38F32  
#define __DEC128_EPSILON__ 1E-33DL  
#define __PTRDIFF_MAX__ 0x7fffffff  
#define __FLT32_HAS_QUIET_NAN__ 1  
#define __GNUG__ 8  
#define __LONG_LONG_MAX__ 0x7fffffffffffffffLL  
#define __SIZEOF_SIZE_T__ 4  
#define __cpp_rvalue_reference 200610  
#define __cpp_nsdmi 200809  
#define __SIZEOF_WINT_T__ 4  
#define __LONG_LONG_WIDTH__ 64  
#define __cpp_initializer_lists 200806  
#define __FLT32_MAX_EXP__ 128  
#define __cpp_hex_float 201603  
#define __GCC_HAVE_DWARF2_CFI_ASM 1  
#define __GXX_ABI_VERSION 1013  
#define __FLT_MIN_EXP__ (-125)  
#define __cpp_lambdas 200907  
#define __INT_FAST64_TYPE__ long long int  
#define __FLT64_DENORM_MIN__ 4.9406564584124654e-324F64  
#define __DBL_MIN__ double(2.2250738585072014e-308L)  
#define __FLT32X_EPSILON__ 2.2204460492503131e-16F32x  
#define __FLT64_MIN_10_EXP__ (-307)  
#define __DEC128_MIN__ 1E-6143DL  
#define __REGISTER_PREFIX__   
#define __UINT16_MAX__ 0xffff  
#define __DBL_HAS_DENORM__ 1  
#define __FLT32_MIN__ 1.1754943508222875e-38F32  
#define __UINT8_TYPE__ unsigned char  
#define __NO_INLINE__ 1  
#define __FLT_MANT_DIG__ 24  
#define __LDBL_DECIMAL_DIG__ 17  
#define __VERSION__ "8.3.0"  
#define __UINT64_C(c) c ## ULL  
#define __cpp_unicode_characters 200704  
#define _STDC_PREDEF_H 1  
#define __cpp_decltype_auto 201304  
#define __GCC_ATOMIC_INT_LOCK_FREE 1  
#define __FLT32_MANT_DIG__ 24  
#define __FLOAT_WORD_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __SCHAR_WIDTH__ 8  
#define __INT32_C(c) c  
#define __DEC64_EPSILON__ 1E-15DD  
#define __ORDER_PDP_ENDIAN__ 3412  
#define __DEC128_MIN_EXP__ (-6142)  
#define __FLT32_MAX_10_EXP__ 38  
#define __INT_FAST32_TYPE__ int  
#define __UINT_LEAST16_TYPE__ short unsigned int  
#define unix 1  
#define __INT16_MAX__ 0x7fff  
#define __cpp_rtti 199711  
#define __SIZE_TYPE__ unsigned int  
#define __UINT64_MAX__ 0xffffffffffffffffULL  
#define __INT8_TYPE__ signed char  
#define __cpp_digit_separators 201309  
#define __ELF__ 1  
#define __FLT_RADIX__ 2  
#define __INT_LEAST16_TYPE__ short int  
#define __LDBL_EPSILON__ 2.2204460492503131e-16L  
#define __UINTMAX_C(c) c ## ULL  
#define __SIG_ATOMIC_MAX__ 0x7fffffff  
#define __GCC_ATOMIC_WCHAR_T_LOCK_FREE 1  
#define __SIZEOF_PTRDIFF_T__ 4  
#define __FLT32X_MANT_DIG__ 53  
#define __FLT32X_MIN_EXP__ (-1021)  
#define __DEC32_SUBNORMAL_MIN__ 0.000001E-95DF  
#define __INT_FAST16_MAX__ 0x7fffffff  
#define __FLT64_DIG__ 15  
#define __UINT_FAST32_MAX__ 0xffffffffU  
#define __UINT_LEAST64_TYPE__ long long unsigned int  
#define __FLT_HAS_QUIET_NAN__ 1  
#define __FLT_MAX_10_EXP__ 38  
#define __LONG_MAX__ 0x7fffffffL  
#define __DEC128_SUBNORMAL_MIN__ 0.000000000000000000000000000000001E-6143DL  
#define __FLT_HAS_INFINITY__ 1  
#define __cpp_unicode_literals 200710  
#define __UINT_FAST16_TYPE__ unsigned int  
#define __DEC64_MAX__ 9.999999999999999E384DD  
#define __INT_FAST32_WIDTH__ 32  
#define __CHAR16_TYPE__ short unsigned int  
#define __PRAGMA_REDEFINE_EXTNAME 1  
#define __SIZE_WIDTH__ 32  
#define __INT_LEAST16_MAX__ 0x7fff  
#define __DEC64_MANT_DIG__ 16  
#define __INT64_MAX__ 0x7fffffffffffffffLL  
#define __UINT_LEAST32_MAX__ 0xffffffffU  
#define __FLT32_DENORM_MIN__ 1.4012984643248171e-45F32  
#define __GCC_ATOMIC_LONG_LOCK_FREE 1  
#define __SIG_ATOMIC_WIDTH__ 32  
#define __INT_LEAST64_TYPE__ long long int  
#define __INT16_TYPE__ short int  
#define __INT_LEAST8_TYPE__ signed char  
#define __DEC32_MAX_EXP__ 97  
#define __INT_FAST8_MAX__ 0x7f  
#define __INTPTR_MAX__ 0x7fffffff  
#define __cpp_sized_deallocation 201309  
#define linux 1  
#define __cpp_range_based_for 200907  
#define __FLT64_HAS_QUIET_NAN__ 1  
#define __FLT32_MIN_10_EXP__ (-37)  
#define __EXCEPTIONS 1  
#define __nios2_little_endian 1  
#define __LDBL_MANT_DIG__ 53  
#define __DBL_HAS_QUIET_NAN__ 1  
#define __FLT64_HAS_INFINITY__ 1  
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
#define __UINT_FAST64_TYPE__ long long unsigned int  
#define __INT_MAX__ 0x7fffffff  
#define __INT64_TYPE__ long long int  
#define __FLT_MAX_EXP__ 128  
#define __DBL_MANT_DIG__ 53  
#define __cpp_inheriting_constructors 201511  
#define __INT_LEAST64_MAX__ 0x7fffffffffffffffLL  
#define __DEC64_MIN__ 1E-383DD  
#define __WINT_TYPE__ unsigned int  
#define __UINT_LEAST32_TYPE__ unsigned int  
#define __SIZEOF_SHORT__ 2  
#define __LDBL_MIN_EXP__ (-1021)  
#define __FLT64_MAX__ 1.7976931348623157e+308F64  
#define __WINT_WIDTH__ 32  
#define __INT_LEAST8_MAX__ 0x7f  
#define __FLT32X_MAX_10_EXP__ 308  
#define __LDBL_MAX_10_EXP__ 308  
#define __ATOMIC_RELAXED 0  
#define __DBL_EPSILON__ double(2.2204460492503131e-16L)  
#define nios2 1  
#define __UINT8_C(c) c  
#define __FLT64_MAX_EXP__ 1024  
#define __INT_LEAST32_TYPE__ int  
#define __SIZEOF_WCHAR_T__ 4  
#define NIOS2 1  
#define __INT_FAST8_TYPE__ signed char  
#define __GNUC_STDC_INLINE__ 1  
#define __FLT64_HAS_DENORM__ 1  
#define __FLT32_EPSILON__ 1.1920928955078125e-7F32  
#define __DBL_DECIMAL_DIG__ 17  
#define __STDC_UTF_32__ 1  
#define __INT_FAST8_WIDTH__ 8  
#define __DEC_EVAL_METHOD__ 2  
#define __FLT32X_MAX__ 1.7976931348623157e+308F32x  
#define __ORDER_BIG_ENDIAN__ 4321  
#define __cpp_runtime_arrays 198712  
#define __UINT64_TYPE__ long long unsigned int  
#define __UINT32_C(c) c ## U  
#define __INTMAX_MAX__ 0x7fffffffffffffffLL  
#define __cpp_alias_templates 200704  
#define __BYTE_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __FLT_DENORM_MIN__ 1.4012984643248171e-45F  
#define __INT8_MAX__ 0x7f  
#define __LONG_WIDTH__ 32  
#define __UINT_FAST32_TYPE__ unsigned int  
#define __CHAR32_TYPE__ unsigned int  
#define __FLT_MAX__ 3.4028234663852886e+38F  
#define __cpp_constexpr 201304  
#define __INT32_TYPE__ int  
#define __SIZEOF_DOUBLE__ 8  
#define __cpp_exceptions 199711  
#define __FLT_MIN_10_EXP__ (-37)  
#define __FLT64_MIN__ 2.2250738585072014e-308F64  
#define __INT_LEAST32_WIDTH__ 32  
#define __INTMAX_TYPE__ long long int  
#define __DEC128_MAX_EXP__ 6145  
#define __FLT32X_HAS_QUIET_NAN__ 1  
#define __ATOMIC_CONSUME 1  
#define __GNUC_MINOR__ 3  
#define __INT_FAST16_WIDTH__ 32  
#define __UINTMAX_MAX__ 0xffffffffffffffffULL  
#define __DEC32_MANT_DIG__ 7  
#define __FLT32X_DENORM_MIN__ 4.9406564584124654e-324F32x  
#define __DBL_MAX_10_EXP__ 308  
#define __LDBL_DENORM_MIN__ 4.9406564584124654e-324L  
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
#define __LDBL_MIN_10_EXP__ (-307)  
#define __SIZEOF_LONG_LONG__ 8  
#define __cpp_user_defined_literals 200809  
#define __GCC_ATOMIC_LLONG_LOCK_FREE 1  
#define __FLT32X_MIN__ 2.2250738585072014e-308F32x  
#define __LDBL_DIG__ 15  
#define __FLT_DECIMAL_DIG__ 9  
#define __UINT_FAST16_MAX__ 0xffffffffU  
#define __GCC_ATOMIC_SHORT_LOCK_FREE 1  
#define __INT_LEAST64_WIDTH__ 64  
#define __UINT_FAST8_TYPE__ unsigned char  
#define _GNU_SOURCE 1  
#define __cpp_init_captures 201304  
#define __ATOMIC_ACQ_REL 4  
#define __ATOMIC_RELEASE 3  
```

---

## Comment 3

> Username: ffontaine  
> Created at: 2020-11-26 07:13:21 UTC  
> Url: https://github.com/boostorg/atomic/issues/42#issuecomment-734119548  

Here is an extract of the build log:  
  
```  
Building the Boost C++ Libraries.  
  
  
    - has stat::st_mtim        : yes  
    - has stat::st_mtimensec   : no  
    - has stat::st_mtimespec   : no  
    - native-atomic-int32-supported : no  
    - native-syslog-supported  : yes  
    - pthread-supports-robust-mutexes : yes  
    - has_icu builds           : no  
    - lockfree boost::atomic_flag : no  
    - x86                      : no  
    - arm                      : no  
    - mips1                    : no  
    - power                    : no  
    - sparc                    : no  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
    - Boost.Config Feature Check: cxx11_noexcept : yes  
    - Boost.Config Feature Check: cxx11_rvalue_references : yes  
    - Boost.Config Feature Check: cxx11_static_assert : yes  
    - std::fstream is moveable and swappable : yes  
    - Has Large File Support   : yes  
```

---

## Comment 4

> Username: Lastique  
> Created at: 2020-11-26 07:40:38 UTC  
> Url: https://github.com/boostorg/atomic/issues/42#issuecomment-734130348  

According to the compiler output, the target does not support atomic operations (all `*_LOCK_FREE` macros equal to 1 instead of 2, which means runtime detection is required). Boost.Atomic requires compile-time knowledge about supported atomic operations, and has a hard requirement of the native atomic operations on bytes. Check if there is a compiler switch (`-march`) for your target that enables native atomic operations.

---

## Comment 5

> Username: ffontaine  
> Created at: 2020-11-26 07:53:34 UTC  
> Url: https://github.com/boostorg/atomic/issues/42#issuecomment-734135769  

Thanks for your feedback, on most architectures, gcc provides "always lock-free" atomic types, but a few architectures (like nios2 or sparc v8) are limited to "sometimes lock-free" types. On buildroot, we manage this through the `BR2_TOOLCHAIN_SUPPORTS_ALWAYS_LOCKFREE_ATOMIC_INTS` variable:  
  
```  
# Atomic types can be:  
#  - never lock-free  
#  - sometimes lock-free  
#  - always lock-free  
# see https://en.cppreference.com/w/c/atomic/ATOMIC_LOCK_FREE_consts  
#  
# On most architectures, gcc provides "always lock-free" atomic types,  
# but a few architectures are limited to "sometimes lock-free"  
# types. This hidden option allows to know if the architecture  
# provides "always lock-free" atomic types.  
config BR2_TOOLCHAIN_SUPPORTS_ALWAYS_LOCKFREE_ATOMIC_INTS  
	bool  
	default y  
	depends on !BR2_nios2  
	depends on !BR2_ARM_CPU_ARMV4  
	depends on !BR2_ARM_CPU_ARMV5  
	depends on !BR2_sparc_v8  
	depends on !BR2_m68k_cf5208  
```  
  
I'll disable atomic if `BR2_TOOLCHAIN_SUPPORTS_ALWAYS_LOCKFREE_ATOMIC_INTS` is false. Can you confirm that this hard requirement on native atomic operations on bytes has been added in boost 1.74.0 or should I backport this requirement on previous boost versions?

---

## Comment 6

> Username: Lastique  
> Created at: 2020-11-26 07:59:19 UTC  
> Url: https://github.com/boostorg/atomic/issues/42#issuecomment-734138144  

Yes, the requirement appeared in 1.74.

---

## Comment 7

> Username: ffontaine  
> Created at: 2020-11-26 08:04:33 UTC  
> Url: https://github.com/boostorg/atomic/issues/42#issuecomment-734140374  

Thanks for your help then this issue can be closed.
