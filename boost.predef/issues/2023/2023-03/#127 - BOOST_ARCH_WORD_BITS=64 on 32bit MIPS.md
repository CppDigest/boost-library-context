# #127 - BOOST_ARCH_WORD_BITS=64 on 32bit MIPS [Open]

> Username: Kojoley  
> Created at: 2023-03-23 10:37:21 UTC  
> Updated at: 2023-03-23 10:37:21 UTC  
> Url: https://github.com/boostorg/predef/issues/127  

The logic `__mips >= 3` is wrong.  
https://github.com/boostorg/predef/blob/e508ed842c153b5dd4858e2cdafd58d2ede418d4/include/boost/predef/architecture/mips.h#L46  
https://github.com/boostorg/predef/blob/e508ed842c153b5dd4858e2cdafd58d2ede418d4/include/boost/predef/architecture/mips.h#L70-L72  
  
GCC --target=mipsel-linux-gnu --with-arch-32=mips32r2  
```c  
#define __ACCUM_EPSILON__ 0x1P-15K  
#define __ACCUM_FBIT__ 15  
#define __ACCUM_IBIT__ 16  
#define __ACCUM_MAX__ 0X7FFFFFFFP-15K  
#define __ACCUM_MIN__ (-0X1P15K-0X1P15K)  
#define __ATOMIC_ACQ_REL 4  
#define __ATOMIC_ACQUIRE 2  
#define __ATOMIC_CONSUME 1  
#define __ATOMIC_RELAXED 0  
#define __ATOMIC_SEQ_CST 5  
#define __BIGGEST_ALIGNMENT__ 8  
#define __BYTE_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __CHAR_BIT__ 8  
#define __CHAR16_TYPE__ short unsigned int  
#define __CHAR32_TYPE__ unsigned int  
#define __DA_FBIT__ 31  
#define __DA_IBIT__ 32  
#define __DBL_DECIMAL_DIG__ 17  
#define __DBL_DENORM_MIN__ ((double)4.9406564584124654e-324L)  
#define __DBL_DIG__ 15  
#define __DBL_EPSILON__ ((double)2.2204460492503131e-16L)  
#define __DBL_HAS_DENORM__ 1  
#define __DBL_HAS_INFINITY__ 1  
#define __DBL_HAS_QUIET_NAN__ 1  
#define __DBL_MANT_DIG__ 53  
#define __DBL_MAX__ ((double)1.7976931348623157e+308L)  
#define __DBL_MAX_10_EXP__ 308  
#define __DBL_MAX_EXP__ 1024  
#define __DBL_MIN__ ((double)2.2250738585072014e-308L)  
#define __DBL_MIN_10_EXP__ (-307)  
#define __DBL_MIN_EXP__ (-1021)  
#define __DBL_NORM_MAX__ ((double)1.7976931348623157e+308L)  
#define __DEC_EVAL_METHOD__ 2  
#define __DECIMAL_DIG__ 17  
#define __DQ_FBIT__ 63  
#define __DQ_IBIT__ 0  
#define __ELF__ 1  
#define __FINITE_MATH_ONLY__ 0  
#define __FLOAT_WORD_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __FLT_DECIMAL_DIG__ 9  
#define __FLT_DENORM_MIN__ 1.4012984643248171e-45F  
#define __FLT_DIG__ 6  
#define __FLT_EPSILON__ 1.1920928955078125e-7F  
#define __FLT_EVAL_METHOD__ 0  
#define __FLT_EVAL_METHOD_TS_18661_3__ 0  
#define __FLT_HAS_DENORM__ 1  
#define __FLT_HAS_INFINITY__ 1  
#define __FLT_HAS_QUIET_NAN__ 1  
#define __FLT_MANT_DIG__ 24  
#define __FLT_MAX__ 3.4028234663852886e+38F  
#define __FLT_MAX_10_EXP__ 38  
#define __FLT_MAX_EXP__ 128  
#define __FLT_MIN__ 1.1754943508222875e-38F  
#define __FLT_MIN_10_EXP__ (-37)  
#define __FLT_MIN_EXP__ (-125)  
#define __FLT_NORM_MAX__ 3.4028234663852886e+38F  
#define __FLT_RADIX__ 2  
#define __FLT32_DECIMAL_DIG__ 9  
#define __FLT32_DENORM_MIN__ 1.4012984643248171e-45F32  
#define __FLT32_DIG__ 6  
#define __FLT32_EPSILON__ 1.1920928955078125e-7F32  
#define __FLT32_HAS_DENORM__ 1  
#define __FLT32_HAS_INFINITY__ 1  
#define __FLT32_HAS_QUIET_NAN__ 1  
#define __FLT32_MANT_DIG__ 24  
#define __FLT32_MAX__ 3.4028234663852886e+38F32  
#define __FLT32_MAX_10_EXP__ 38  
#define __FLT32_MAX_EXP__ 128  
#define __FLT32_MIN__ 1.1754943508222875e-38F32  
#define __FLT32_MIN_10_EXP__ (-37)  
#define __FLT32_MIN_EXP__ (-125)  
#define __FLT32_NORM_MAX__ 3.4028234663852886e+38F32  
#define __FLT32X_DECIMAL_DIG__ 17  
#define __FLT32X_DENORM_MIN__ 4.9406564584124654e-324F32x  
#define __FLT32X_DIG__ 15  
#define __FLT32X_EPSILON__ 2.2204460492503131e-16F32x  
#define __FLT32X_HAS_DENORM__ 1  
#define __FLT32X_HAS_INFINITY__ 1  
#define __FLT32X_HAS_QUIET_NAN__ 1  
#define __FLT32X_MANT_DIG__ 53  
#define __FLT32X_MAX__ 1.7976931348623157e+308F32x  
#define __FLT32X_MAX_10_EXP__ 308  
#define __FLT32X_MAX_EXP__ 1024  
#define __FLT32X_MIN__ 2.2250738585072014e-308F32x  
#define __FLT32X_MIN_10_EXP__ (-307)  
#define __FLT32X_MIN_EXP__ (-1021)  
#define __FLT32X_NORM_MAX__ 1.7976931348623157e+308F32x  
#define __FLT64_DECIMAL_DIG__ 17  
#define __FLT64_DENORM_MIN__ 4.9406564584124654e-324F64  
#define __FLT64_DIG__ 15  
#define __FLT64_EPSILON__ 2.2204460492503131e-16F64  
#define __FLT64_HAS_DENORM__ 1  
#define __FLT64_HAS_INFINITY__ 1  
#define __FLT64_HAS_QUIET_NAN__ 1  
#define __FLT64_MANT_DIG__ 53  
#define __FLT64_MAX__ 1.7976931348623157e+308F64  
#define __FLT64_MAX_10_EXP__ 308  
#define __FLT64_MAX_EXP__ 1024  
#define __FLT64_MIN__ 2.2250738585072014e-308F64  
#define __FLT64_MIN_10_EXP__ (-307)  
#define __FLT64_MIN_EXP__ (-1021)  
#define __FLT64_NORM_MAX__ 1.7976931348623157e+308F64  
#define __FRACT_EPSILON__ 0x1P-15R  
#define __FRACT_FBIT__ 15  
#define __FRACT_IBIT__ 0  
#define __FRACT_MAX__ 0X7FFFP-15R  
#define __FRACT_MIN__ (-0.5R-0.5R)  
#define __GCC_ATOMIC_BOOL_LOCK_FREE 2  
#define __GCC_ATOMIC_CHAR_LOCK_FREE 2  
#define __GCC_ATOMIC_CHAR16_T_LOCK_FREE 2  
#define __GCC_ATOMIC_CHAR32_T_LOCK_FREE 2  
#define __GCC_ATOMIC_INT_LOCK_FREE 2  
#define __GCC_ATOMIC_LLONG_LOCK_FREE 1  
#define __GCC_ATOMIC_LONG_LOCK_FREE 2  
#define __GCC_ATOMIC_POINTER_LOCK_FREE 2  
#define __GCC_ATOMIC_SHORT_LOCK_FREE 2  
#define __GCC_ATOMIC_TEST_AND_SET_TRUEVAL 1  
#define __GCC_ATOMIC_WCHAR_T_LOCK_FREE 2  
#define __GCC_HAVE_BUILTIN_MIPS_CACHE 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_1 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_2 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_4 1  
#define __GCC_IEC_559 1  
#define __GCC_IEC_559_COMPLEX 1  
#define __gnu_linux__ 1  
#define __GNUC__ 10  
#define __GNUC_MINOR__ 2  
#define __GNUC_PATCHLEVEL__ 1  
#define __GNUC_STDC_INLINE__ 1  
#define __GXX_ABI_VERSION 1014  
#define __HA_FBIT__ 7  
#define __HA_IBIT__ 8  
#define __HQ_FBIT__ 15  
#define __HQ_IBIT__ 0  
#define __INT_FAST16_MAX__ 0x7fffffff  
#define __INT_FAST16_TYPE__ int  
#define __INT_FAST16_WIDTH__ 32  
#define __INT_FAST32_MAX__ 0x7fffffff  
#define __INT_FAST32_TYPE__ int  
#define __INT_FAST32_WIDTH__ 32  
#define __INT_FAST64_MAX__ 0x7fffffffffffffffLL  
#define __INT_FAST64_TYPE__ long long int  
#define __INT_FAST64_WIDTH__ 64  
#define __INT_FAST8_MAX__ 0x7f  
#define __INT_FAST8_TYPE__ signed char  
#define __INT_FAST8_WIDTH__ 8  
#define __INT_LEAST16_MAX__ 0x7fff  
#define __INT_LEAST16_TYPE__ short int  
#define __INT_LEAST16_WIDTH__ 16  
#define __INT_LEAST32_MAX__ 0x7fffffff  
#define __INT_LEAST32_TYPE__ int  
#define __INT_LEAST32_WIDTH__ 32  
#define __INT_LEAST64_MAX__ 0x7fffffffffffffffLL  
#define __INT_LEAST64_TYPE__ long long int  
#define __INT_LEAST64_WIDTH__ 64  
#define __INT_LEAST8_MAX__ 0x7f  
#define __INT_LEAST8_TYPE__ signed char  
#define __INT_LEAST8_WIDTH__ 8  
#define __INT_MAX__ 0x7fffffff  
#define __INT_WIDTH__ 32  
#define __INT16_C(c) c  
#define __INT16_MAX__ 0x7fff  
#define __INT16_TYPE__ short int  
#define __INT32_C(c) c  
#define __INT32_MAX__ 0x7fffffff  
#define __INT32_TYPE__ int  
#define __INT64_C(c) c ## LL  
#define __INT64_MAX__ 0x7fffffffffffffffLL  
#define __INT64_TYPE__ long long int  
#define __INT8_C(c) c  
#define __INT8_MAX__ 0x7f  
#define __INT8_TYPE__ signed char  
#define __INTMAX_C(c) c ## LL  
#define __INTMAX_MAX__ 0x7fffffffffffffffLL  
#define __INTMAX_TYPE__ long long int  
#define __INTMAX_WIDTH__ 64  
#define __INTPTR_MAX__ 0x7fffffff  
#define __INTPTR_TYPE__ int  
#define __INTPTR_WIDTH__ 32  
#define __LACCUM_EPSILON__ 0x1P-31LK  
#define __LACCUM_FBIT__ 31  
#define __LACCUM_IBIT__ 32  
#define __LACCUM_MAX__ 0X7FFFFFFFFFFFFFFFP-31LK  
#define __LACCUM_MIN__ (-0X1P31LK-0X1P31LK)  
#define __LANGUAGE_C 1  
#define __LANGUAGE_C__ 1  
#define __LDBL_DECIMAL_DIG__ 17  
#define __LDBL_DENORM_MIN__ 4.9406564584124654e-324L  
#define __LDBL_DIG__ 15  
#define __LDBL_EPSILON__ 2.2204460492503131e-16L  
#define __LDBL_HAS_DENORM__ 1  
#define __LDBL_HAS_INFINITY__ 1  
#define __LDBL_HAS_QUIET_NAN__ 1  
#define __LDBL_MANT_DIG__ 53  
#define __LDBL_MAX__ 1.7976931348623157e+308L  
#define __LDBL_MAX_10_EXP__ 308  
#define __LDBL_MAX_EXP__ 1024  
#define __LDBL_MIN__ 2.2250738585072014e-308L  
#define __LDBL_MIN_10_EXP__ (-307)  
#define __LDBL_MIN_EXP__ (-1021)  
#define __LDBL_NORM_MAX__ 1.7976931348623157e+308L  
#define __LFRACT_EPSILON__ 0x1P-31LR  
#define __LFRACT_FBIT__ 31  
#define __LFRACT_IBIT__ 0  
#define __LFRACT_MAX__ 0X7FFFFFFFP-31LR  
#define __LFRACT_MIN__ (-0.5LR-0.5LR)  
#define __linux 1  
#define __linux__ 1  
#define __LLACCUM_EPSILON__ 0x1P-31LLK  
#define __LLACCUM_FBIT__ 31  
#define __LLACCUM_IBIT__ 32  
#define __LLACCUM_MAX__ 0X7FFFFFFFFFFFFFFFP-31LLK  
#define __LLACCUM_MIN__ (-0X1P31LLK-0X1P31LLK)  
#define __LLFRACT_EPSILON__ 0x1P-63LLR  
#define __LLFRACT_FBIT__ 63  
#define __LLFRACT_IBIT__ 0  
#define __LLFRACT_MAX__ 0X7FFFFFFFFFFFFFFFP-63LLR  
#define __LLFRACT_MIN__ (-0.5LLR-0.5LLR)  
#define __LONG_LONG_MAX__ 0x7fffffffffffffffLL  
#define __LONG_LONG_WIDTH__ 64  
#define __LONG_MAX__ 0x7fffffffL  
#define __LONG_WIDTH__ 32  
#define __mips 32  
#define __mips__ 1  
#define __mips_abicalls 1  
#define __mips_fpr 0  
#define __mips_hard_float 1  
#define __mips_isa_rev 2  
#define __mips_no_lxc1_sxc1 1  
#define __mips_no_madd4 1  
#define __MIPSEL 1  
#define __MIPSEL__ 1  
#define __NO_INLINE__ 1  
#define __ORDER_BIG_ENDIAN__ 4321  
#define __ORDER_LITTLE_ENDIAN__ 1234  
#define __ORDER_PDP_ENDIAN__ 3412  
#define __pic__ 1  
#define __PIC__ 1  
#define __PIE__ 2  
#define __pie__ 2  
#define __PRAGMA_REDEFINE_EXTNAME 1  
#define __PTRDIFF_MAX__ 0x7fffffff  
#define __PTRDIFF_TYPE__ int  
#define __PTRDIFF_WIDTH__ 32  
#define __QQ_FBIT__ 7  
#define __QQ_IBIT__ 0  
#define __R3000 1  
#define __R3000__ 1  
#define __REGISTER_PREFIX__ $  
#define __SA_FBIT__ 15  
#define __SA_IBIT__ 16  
#define __SACCUM_EPSILON__ 0x1P-7HK  
#define __SACCUM_FBIT__ 7  
#define __SACCUM_IBIT__ 8  
#define __SACCUM_MAX__ 0X7FFFP-7HK  
#define __SACCUM_MIN__ (-0X1P7HK-0X1P7HK)  
#define __SCHAR_MAX__ 0x7f  
#define __SCHAR_WIDTH__ 8  
#define __SFRACT_EPSILON__ 0x1P-7HR  
#define __SFRACT_FBIT__ 7  
#define __SFRACT_IBIT__ 0  
#define __SFRACT_MAX__ 0X7FP-7HR  
#define __SFRACT_MIN__ (-0.5HR-0.5HR)  
#define __SHRT_MAX__ 0x7fff  
#define __SHRT_WIDTH__ 16  
#define __SIG_ATOMIC_MAX__ 0x7fffffff  
#define __SIG_ATOMIC_MIN__ (-__SIG_ATOMIC_MAX__ - 1)  
#define __SIG_ATOMIC_TYPE__ int  
#define __SIG_ATOMIC_WIDTH__ 32  
#define __SIZE_MAX__ 0xffffffffU  
#define __SIZE_TYPE__ unsigned int  
#define __SIZE_WIDTH__ 32  
#define __SIZEOF_DOUBLE__ 8  
#define __SIZEOF_FLOAT__ 4  
#define __SIZEOF_INT__ 4  
#define __SIZEOF_LONG__ 4  
#define __SIZEOF_LONG_DOUBLE__ 8  
#define __SIZEOF_LONG_LONG__ 8  
#define __SIZEOF_POINTER__ 4  
#define __SIZEOF_PTRDIFF_T__ 4  
#define __SIZEOF_SHORT__ 2  
#define __SIZEOF_SIZE_T__ 4  
#define __SIZEOF_WCHAR_T__ 4  
#define __SIZEOF_WINT_T__ 4  
#define __SQ_FBIT__ 31  
#define __SQ_IBIT__ 0  
#define __STDC__ 1  
#define __STDC_HOSTED__ 1  
#define __STDC_IEC_559__ 1  
#define __STDC_IEC_559_COMPLEX__ 1  
#define __STDC_ISO_10646__ 201706L  
#define __STDC_UTF_16__ 1  
#define __STDC_UTF_32__ 1  
#define __STDC_VERSION__ 201710L  
#define __TA_FBIT__ 63  
#define __TA_IBIT__ 64  
#define __TQ_FBIT__ 127  
#define __TQ_IBIT__ 0  
#define __UACCUM_EPSILON__ 0x1P-16UK  
#define __UACCUM_FBIT__ 16  
#define __UACCUM_IBIT__ 16  
#define __UACCUM_MAX__ 0XFFFFFFFFP-16UK  
#define __UACCUM_MIN__ 0.0UK  
#define __UDA_FBIT__ 32  
#define __UDA_IBIT__ 32  
#define __UDQ_FBIT__ 64  
#define __UDQ_IBIT__ 0  
#define __UFRACT_EPSILON__ 0x1P-16UR  
#define __UFRACT_FBIT__ 16  
#define __UFRACT_IBIT__ 0  
#define __UFRACT_MAX__ 0XFFFFP-16UR  
#define __UFRACT_MIN__ 0.0UR  
#define __UHA_FBIT__ 8  
#define __UHA_IBIT__ 8  
#define __UHQ_FBIT__ 16  
#define __UHQ_IBIT__ 0  
#define __UINT_FAST16_MAX__ 0xffffffffU  
#define __UINT_FAST16_TYPE__ unsigned int  
#define __UINT_FAST32_MAX__ 0xffffffffU  
#define __UINT_FAST32_TYPE__ unsigned int  
#define __UINT_FAST64_MAX__ 0xffffffffffffffffULL  
#define __UINT_FAST64_TYPE__ long long unsigned int  
#define __UINT_FAST8_MAX__ 0xff  
#define __UINT_FAST8_TYPE__ unsigned char  
#define __UINT_LEAST16_MAX__ 0xffff  
#define __UINT_LEAST16_TYPE__ short unsigned int  
#define __UINT_LEAST32_MAX__ 0xffffffffU  
#define __UINT_LEAST32_TYPE__ unsigned int  
#define __UINT_LEAST64_MAX__ 0xffffffffffffffffULL  
#define __UINT_LEAST64_TYPE__ long long unsigned int  
#define __UINT_LEAST8_MAX__ 0xff  
#define __UINT_LEAST8_TYPE__ unsigned char  
#define __UINT16_C(c) c  
#define __UINT16_MAX__ 0xffff  
#define __UINT16_TYPE__ short unsigned int  
#define __UINT32_C(c) c ## U  
#define __UINT32_MAX__ 0xffffffffU  
#define __UINT32_TYPE__ unsigned int  
#define __UINT64_C(c) c ## ULL  
#define __UINT64_MAX__ 0xffffffffffffffffULL  
#define __UINT64_TYPE__ long long unsigned int  
#define __UINT8_C(c) c  
#define __UINT8_MAX__ 0xff  
#define __UINT8_TYPE__ unsigned char  
#define __UINTMAX_C(c) c ## ULL  
#define __UINTMAX_MAX__ 0xffffffffffffffffULL  
#define __UINTMAX_TYPE__ long long unsigned int  
#define __UINTPTR_MAX__ 0xffffffffU  
#define __UINTPTR_TYPE__ unsigned int  
#define __ULACCUM_EPSILON__ 0x1P-32ULK  
#define __ULACCUM_FBIT__ 32  
#define __ULACCUM_IBIT__ 32  
#define __ULACCUM_MAX__ 0XFFFFFFFFFFFFFFFFP-32ULK  
#define __ULACCUM_MIN__ 0.0ULK  
#define __ULFRACT_EPSILON__ 0x1P-32ULR  
#define __ULFRACT_FBIT__ 32  
#define __ULFRACT_IBIT__ 0  
#define __ULFRACT_MAX__ 0XFFFFFFFFP-32ULR  
#define __ULFRACT_MIN__ 0.0ULR  
#define __ULLACCUM_EPSILON__ 0x1P-32ULLK  
#define __ULLACCUM_FBIT__ 32  
#define __ULLACCUM_IBIT__ 32  
#define __ULLACCUM_MAX__ 0XFFFFFFFFFFFFFFFFP-32ULLK  
#define __ULLACCUM_MIN__ 0.0ULLK  
#define __ULLFRACT_EPSILON__ 0x1P-64ULLR  
#define __ULLFRACT_FBIT__ 64  
#define __ULLFRACT_IBIT__ 0  
#define __ULLFRACT_MAX__ 0XFFFFFFFFFFFFFFFFP-64ULLR  
#define __ULLFRACT_MIN__ 0.0ULLR  
#define __unix 1  
#define __unix__ 1  
#define __UQQ_FBIT__ 8  
#define __UQQ_IBIT__ 0  
#define __USA_FBIT__ 16  
#define __USA_IBIT__ 16  
#define __USACCUM_EPSILON__ 0x1P-8UHK  
#define __USACCUM_FBIT__ 8  
#define __USACCUM_IBIT__ 8  
#define __USACCUM_MAX__ 0XFFFFP-8UHK  
#define __USACCUM_MIN__ 0.0UHK  
#define __USER_LABEL_PREFIX__   
#define __USFRACT_EPSILON__ 0x1P-8UHR  
#define __USFRACT_FBIT__ 8  
#define __USFRACT_IBIT__ 0  
#define __USFRACT_MAX__ 0XFFP-8UHR  
#define __USFRACT_MIN__ 0.0UHR  
#define __USQ_FBIT__ 32  
#define __USQ_IBIT__ 0  
#define __UTA_FBIT__ 64  
#define __UTA_IBIT__ 64  
#define __UTQ_FBIT__ 128  
#define __UTQ_IBIT__ 0  
#define __VERSION__ "10.2.1 20210110"  
#define __WCHAR_MAX__ 0x7fffffff  
#define __WCHAR_MIN__ (-__WCHAR_MAX__ - 1)  
#define __WCHAR_TYPE__ int  
#define __WCHAR_WIDTH__ 32  
#define __WINT_MAX__ 0xffffffffU  
#define __WINT_MIN__ 0U  
#define __WINT_TYPE__ unsigned int  
#define __WINT_WIDTH__ 32  
#define _ABIO32 1  
#define _LANGUAGE_C 1  
#define _mips 1  
#define _MIPS_ARCH "mips32r2"  
#define _MIPS_ARCH_MIPS32R2 1  
#define _MIPS_FPSET 16  
#define _MIPS_ISA _MIPS_ISA_MIPS32  
#define _MIPS_SIM _ABIO32  
#define _MIPS_SPFPSET 16  
#define _MIPS_SZINT 32  
#define _MIPS_SZLONG 32  
#define _MIPS_SZPTR 32  
#define _MIPS_TUNE "mips32r2"  
#define _MIPS_TUNE_MIPS32R2 1  
#define _MIPSEL 1  
#define _R3000 1  
#define _STDC_PREDEF_H 1  
#define BOOST_ARCH_ALPHA BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_ALPHA_NAME "DEC Alpha"  
#define BOOST_ARCH_ARM BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_ARM_NAME "ARM"  
#define BOOST_ARCH_BLACKFIN BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_BLACKFIN_NAME "Blackfin"  
#define BOOST_ARCH_CONVEX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_CONVEX_NAME "Convex Computer"  
#define BOOST_ARCH_E2K BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_E2K_NAME "E2K"  
#define BOOST_ARCH_IA64 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_IA64_NAME "Intel Itanium 64"  
#define BOOST_ARCH_LOONGARCH BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_LOONGARCH_NAME "LoongArch"  
#define BOOST_ARCH_M68K BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_M68K_NAME "Motorola 68k"  
#define BOOST_ARCH_MIPS BOOST_VERSION_NUMBER(__mips,0,0)  
#define BOOST_ARCH_MIPS_AVAILABLE   
#define BOOST_ARCH_MIPS_NAME "MIPS"  
#define BOOST_ARCH_PARISC BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PARISC_NAME "HP/PA RISC"  
#define BOOST_ARCH_PPC BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PPC_64 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PPC_64_NAME "PowerPC64"  
#define BOOST_ARCH_PPC_NAME "PowerPC"  
#define BOOST_ARCH_PTX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PTX_NAME "PTX"  
#define BOOST_ARCH_PWR BOOST_ARCH_RS6000  
#define BOOST_ARCH_PWR_NAME BOOST_ARCH_RS6000_NAME  
#define BOOST_ARCH_PYRAMID BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PYRAMID_NAME "Pyramid 9810"  
#define BOOST_ARCH_RISCV BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_RISCV_NAME "RISC-V"  
#define BOOST_ARCH_RS6000 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_RS6000_NAME "RS/6000"  
#define BOOST_ARCH_SH BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_SH_NAME "SuperH"  
#define BOOST_ARCH_SPARC BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_SPARC_NAME "SPARC"  
#define BOOST_ARCH_SYS370 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_SYS370_NAME "System/370"  
#define BOOST_ARCH_SYS390 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_SYS390_NAME "System/390"  
#define BOOST_ARCH_WORD_BITS 64  
#define BOOST_ARCH_WORD_BITS_16 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_WORD_BITS_16_NAME "16-bit Word Size"  
#define BOOST_ARCH_WORD_BITS_32 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ARCH_WORD_BITS_32_NAME "32-bit Word Size"  
#define BOOST_ARCH_WORD_BITS_64 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ARCH_WORD_BITS_64_NAME "64-bit Word Size"  
#define BOOST_ARCH_WORD_BITS_NAME "Word Bits"  
#define BOOST_ARCH_X86 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_X86_32 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_X86_32_NAME "Intel x86-32"  
#define BOOST_ARCH_X86_64 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_X86_64_NAME "Intel x86-64"  
#define BOOST_ARCH_X86_NAME "Intel x86"  
#define BOOST_ARCH_Z BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_Z_NAME "z/Architecture"  
#define BOOST_CONFIG_HELPER_MACROS_HPP_INCLUDED   
#define BOOST_DO_JOIN(X,Y) BOOST_DO_JOIN2(X,Y)  
#define BOOST_DO_JOIN2(X,Y) X ##Y  
#define BOOST_DO_STRINGIZE(X) #X  
#define BOOST_JOIN(X,Y) BOOST_DO_JOIN(X, Y)  
#define BOOST_OS_AIX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_AIX_NAME "IBM AIX"  
#define BOOST_OS_AMIGAOS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_AMIGAOS_NAME "AmigaOS"  
#define BOOST_OS_BEOS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BEOS_NAME "BeOS"  
#define BOOST_OS_BSD BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_BSDI BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_BSDI_NAME "BSDi BSD/OS"  
#define BOOST_OS_BSD_DRAGONFLY BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_DRAGONFLY_NAME "DragonFly BSD"  
#define BOOST_OS_BSD_FREE BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_FREE_NAME "Free BSD"  
#define BOOST_OS_BSD_NAME "BSD"  
#define BOOST_OS_BSD_NET BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_NET_NAME "NetBSD"  
#define BOOST_OS_BSD_OPEN BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_OPEN_NAME "OpenBSD"  
#define BOOST_OS_CYGWIN BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_CYGWIN_NAME "Cygwin"  
#define BOOST_OS_HAIKU BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_HAIKU_NAME "Haiku"  
#define BOOST_OS_HPUX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_HPUX_NAME "HP-UX"  
#define BOOST_OS_IOS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_IOS_NAME "iOS"  
#define BOOST_OS_IRIX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_IRIX_NAME "IRIX"  
#define BOOST_OS_LINUX BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_OS_LINUX_AVAILABLE   
#define BOOST_OS_LINUX_NAME "Linux"  
#define BOOST_OS_MACOS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_MACOS_NAME "Mac OS"  
#define BOOST_OS_OS400 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_OS400_NAME "IBM OS/400"  
#define BOOST_OS_QNX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_QNX_NAME "QNX"  
#define BOOST_OS_SOLARIS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_SOLARIS_NAME "Solaris"  
#define BOOST_OS_SVR4 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_SVR4_NAME "SVR4 Environment"  
#define BOOST_OS_UNIX BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_OS_UNIX_AVAILABLE   
#define BOOST_OS_UNIX_NAME "Unix Environment"  
#define BOOST_OS_VMS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_VMS_NAME "VMS"  
#define BOOST_OS_WINDOWS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_WINDOWS_NAME "Microsoft Windows"  
#define BOOST_PREDEF_ARCHITECTURE_ALPHA_H   
#define BOOST_PREDEF_ARCHITECTURE_ARM_H   
#define BOOST_PREDEF_ARCHITECTURE_BLACKFIN_H   
#define BOOST_PREDEF_ARCHITECTURE_CONVEX_H   
#define BOOST_PREDEF_ARCHITECTURE_E2K_H   
#define BOOST_PREDEF_ARCHITECTURE_H   
#define BOOST_PREDEF_ARCHITECTURE_IA64_H   
#define BOOST_PREDEF_ARCHITECTURE_LOONGARCH_H   
#define BOOST_PREDEF_ARCHITECTURE_M68K_H   
#define BOOST_PREDEF_ARCHITECTURE_MIPS_H   
#define BOOST_PREDEF_ARCHITECTURE_PARISC_H   
#define BOOST_PREDEF_ARCHITECTURE_PPC_H   
#define BOOST_PREDEF_ARCHITECTURE_PTX_H   
#define BOOST_PREDEF_ARCHITECTURE_PYRAMID_H   
#define BOOST_PREDEF_ARCHITECTURE_RISCV_H   
#define BOOST_PREDEF_ARCHITECTURE_RS6K_H   
#define BOOST_PREDEF_ARCHITECTURE_SPARC_H   
#define BOOST_PREDEF_ARCHITECTURE_SUPERH_H   
#define BOOST_PREDEF_ARCHITECTURE_SYS370_H   
#define BOOST_PREDEF_ARCHITECTURE_SYS390_H   
#define BOOST_PREDEF_ARCHITECTURE_X86_32_H   
#define BOOST_PREDEF_ARCHITECTURE_X86_64_H   
#define BOOST_PREDEF_ARCHITECTURE_X86_H   
#define BOOST_PREDEF_ARCHITECTURE_Z_H   
#define BOOST_PREDEF_DECLARE_TEST(x,s)   
#define BOOST_PREDEF_DETAIL_OS_DETECTED 1  
#define BOOST_PREDEF_DETAIL_TEST_H   
#define BOOST_PREDEF_MAKE_0X_VRP(V) BOOST_VERSION_NUMBER((V&0xF00)>>8,(V&0xF0)>>4,(V&0xF))  
#define BOOST_PREDEF_MAKE_0X_VRPP(V) BOOST_VERSION_NUMBER((V&0xF000)>>12,(V&0xF00)>>8,(V&0xFF))  
#define BOOST_PREDEF_MAKE_0X_VRRPP000(V) BOOST_VERSION_NUMBER((V&0xF0000000)>>28,(V&0xFF00000)>>20,(V&0xFF000)>>12)  
#define BOOST_PREDEF_MAKE_0X_VRRPPPP(V) BOOST_VERSION_NUMBER((V&0xF000000)>>24,(V&0xFF0000)>>16,(V&0xFFFF))  
#define BOOST_PREDEF_MAKE_0X_VVRP(V) BOOST_VERSION_NUMBER((V&0xFF00)>>8,(V&0xF0)>>4,(V&0xF))  
#define BOOST_PREDEF_MAKE_0X_VVRR(V) BOOST_VERSION_NUMBER((V&0xFF00)>>8,(V&0xFF),0)  
#define BOOST_PREDEF_MAKE_0X_VVRRP(V) BOOST_VERSION_NUMBER((V&0xFF000)>>12,(V&0xFF0)>>4,(V&0xF))  
#define BOOST_PREDEF_MAKE_0X_VVRRPP(V) BOOST_VERSION_NUMBER((V&0xFF0000)>>16,(V&0xFF00)>>8,(V&0xFF))  
#define BOOST_PREDEF_MAKE_10_VPPP(V) BOOST_VERSION_NUMBER(((V)/1000)%10,0,(V)%1000)  
#define BOOST_PREDEF_MAKE_10_VR0(V) BOOST_VERSION_NUMBER(((V)/100)%10,((V)/10)%10,0)  
#define BOOST_PREDEF_MAKE_10_VRP(V) BOOST_VERSION_NUMBER(((V)/100)%10,((V)/10)%10,(V)%10)  
#define BOOST_PREDEF_MAKE_10_VRP000(V) BOOST_VERSION_NUMBER(((V)/100000)%10,((V)/10000)%10,((V)/1000)%10)  
#define BOOST_PREDEF_MAKE_10_VRPP(V) BOOST_VERSION_NUMBER(((V)/1000)%10,((V)/100)%10,(V)%100)  
#define BOOST_PREDEF_MAKE_10_VRPPPP(V) BOOST_VERSION_NUMBER(((V)/100000)%10,((V)/10000)%10,(V)%10000)  
#define BOOST_PREDEF_MAKE_10_VRR(V) BOOST_VERSION_NUMBER(((V)/100)%10,(V)%100,0)  
#define BOOST_PREDEF_MAKE_10_VRR000(V) BOOST_VERSION_NUMBER(((V)/100000)%10,((V)/1000)%100,0)  
#define BOOST_PREDEF_MAKE_10_VRRPP(V) BOOST_VERSION_NUMBER(((V)/10000)%10,((V)/100)%100,(V)%100)  
#define BOOST_PREDEF_MAKE_10_VV00(V) BOOST_VERSION_NUMBER(((V)/100)%100,0,0)  
#define BOOST_PREDEF_MAKE_10_VVPPP(V) BOOST_VERSION_NUMBER(((V)/1000)%100,0,(V)%1000)  
#define BOOST_PREDEF_MAKE_10_VVRR(V) BOOST_VERSION_NUMBER(((V)/100)%100,(V)%100,0)  
#define BOOST_PREDEF_MAKE_10_VVRR00PP00(V) BOOST_VERSION_NUMBER(((V)/100000000)%100,((V)/1000000)%100,((V)/100)%100)  
#define BOOST_PREDEF_MAKE_10_VVRR0PP00(V) BOOST_VERSION_NUMBER(((V)/10000000)%100,((V)/100000)%100,((V)/100)%100)  
#define BOOST_PREDEF_MAKE_10_VVRR0PPPP(V) BOOST_VERSION_NUMBER(((V)/10000000)%100,((V)/100000)%100,(V)%10000)  
#define BOOST_PREDEF_MAKE_10_VVRRP(V) BOOST_VERSION_NUMBER(((V)/1000)%100,((V)/10)%100,(V)%10)  
#define BOOST_PREDEF_MAKE_10_VVRRPP(V) BOOST_VERSION_NUMBER(((V)/10000)%100,((V)/100)%100,(V)%100)  
#define BOOST_PREDEF_MAKE_10_VVRRPPP(V) BOOST_VERSION_NUMBER(((V)/100000)%100,((V)/1000)%100,(V)%1000)  
#define BOOST_PREDEF_MAKE_DATE(Y,M,D) BOOST_VERSION_NUMBER((Y)%10000-1970,(M)%100,(D)%100)  
#define BOOST_PREDEF_MAKE_H   
#define BOOST_PREDEF_MAKE_YYYY(V) BOOST_PREDEF_MAKE_DATE(V,1,1)  
#define BOOST_PREDEF_MAKE_YYYYMM(V) BOOST_PREDEF_MAKE_DATE((V)/100,(V)%100,1)  
#define BOOST_PREDEF_MAKE_YYYYMMDD(V) BOOST_PREDEF_MAKE_DATE(((V)/10000)%10000,((V)/100)%100,(V)%100)  
#define BOOST_PREDEF_OS_AIX_H   
#define BOOST_PREDEF_OS_AMIGAOS_H   
#define BOOST_PREDEF_OS_BEOS_H   
#define BOOST_PREDEF_OS_BSD_BSDI_H   
#define BOOST_PREDEF_OS_BSD_DRAGONFLY_H   
#define BOOST_PREDEF_OS_BSD_FREE_H   
#define BOOST_PREDEF_OS_BSD_H   
#define BOOST_PREDEF_OS_BSD_NET_H   
#define BOOST_PREDEF_OS_BSD_OPEN_H   
#define BOOST_PREDEF_OS_CYGWIN_H   
#define BOOST_PREDEF_OS_H   
#define BOOST_PREDEF_OS_HAIKU_H   
#define BOOST_PREDEF_OS_HPUX_H   
#define BOOST_PREDEF_OS_IOS_H   
#define BOOST_PREDEF_OS_IRIX_H   
#define BOOST_PREDEF_OS_LINUX_H   
#define BOOST_PREDEF_OS_MACOS_H   
#define BOOST_PREDEF_OS_OS400_H   
#define BOOST_PREDEF_OS_QNXNTO_H   
#define BOOST_PREDEF_OS_SOLARIS_H   
#define BOOST_PREDEF_OS_UNIX_H   
#define BOOST_PREDEF_OS_VMS_H   
#define BOOST_PREDEF_OS_WINDOWS_H   
#define BOOST_PREDEF_OTHER_WORD_SIZE_H   
#define BOOST_PREDEF_VERSION_NUMBER_H   
#define BOOST_STRINGIZE(X) BOOST_DO_STRINGIZE(X)  
#define BOOST_VERSION_NUMBER(major,minor,patch) ( (((major)%100)*10000000) + (((minor)%100)*100000) + ((patch)%100000) )  
#define BOOST_VERSION_NUMBER_AVAILABLE BOOST_VERSION_NUMBER_MIN  
#define BOOST_VERSION_NUMBER_MAJOR(N) ( ((N)/10000000)%100 )  
#define BOOST_VERSION_NUMBER_MAX BOOST_VERSION_NUMBER(99,99,99999)  
#define BOOST_VERSION_NUMBER_MIN BOOST_VERSION_NUMBER(0,0,1)  
#define BOOST_VERSION_NUMBER_MINOR(N) ( ((N)/100000)%100 )  
#define BOOST_VERSION_NUMBER_NOT_AVAILABLE BOOST_VERSION_NUMBER_ZERO  
#define BOOST_VERSION_NUMBER_PATCH(N) ( (N)%100000 )  
#define BOOST_VERSION_NUMBER_ZERO BOOST_VERSION_NUMBER(0,0,0)  
#define LANGUAGE_C 1  
#define linux 1  
#define mips 1  
#define MIPSEL 1  
#define R3000 1  
#define unix 1  
```  
  
GCC --target=mips64el-linux-gnuabi64 --with-arch-64=mips64r2  
```c  
#define __ACCUM_EPSILON__ 0x1P-15K  
#define __ACCUM_FBIT__ 15  
#define __ACCUM_IBIT__ 16  
#define __ACCUM_MAX__ 0X7FFFFFFFP-15K  
#define __ACCUM_MIN__ (-0X1P15K-0X1P15K)  
#define __ATOMIC_ACQ_REL 4  
#define __ATOMIC_ACQUIRE 2  
#define __ATOMIC_CONSUME 1  
#define __ATOMIC_RELAXED 0  
#define __ATOMIC_RELEASE 3  
#define __ATOMIC_SEQ_CST 5  
#define __BIGGEST_ALIGNMENT__ 16  
#define __BYTE_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __CHAR_BIT__ 8  
#define __CHAR16_TYPE__ short unsigned int  
#define __CHAR32_TYPE__ unsigned int  
#define __DA_FBIT__ 31  
#define __DA_IBIT__ 32  
#define __DBL_DECIMAL_DIG__ 17  
#define __DBL_DENORM_MIN__ ((double)4.94065645841246544176568792868221372e-324L)  
#define __DBL_DIG__ 15  
#define __DBL_EPSILON__ ((double)2.22044604925031308084726333618164062e-16L)  
#define __DBL_HAS_DENORM__ 1  
#define __DBL_HAS_INFINITY__ 1  
#define __DBL_HAS_QUIET_NAN__ 1  
#define __DBL_MANT_DIG__ 53  
#define __DBL_MAX__ ((double)1.79769313486231570814527423731704357e+308L)  
#define __DBL_MAX_10_EXP__ 308  
#define __DBL_MAX_EXP__ 1024  
#define __DBL_MIN__ ((double)2.22507385850720138309023271733240406e-308L)  
#define __DBL_MIN_10_EXP__ (-307)  
#define __DBL_MIN_EXP__ (-1021)  
#define __DBL_NORM_MAX__ ((double)1.79769313486231570814527423731704357e+308L)  
#define __DEC_EVAL_METHOD__ 2  
#define __DECIMAL_DIG__ 36  
#define __DQ_FBIT__ 63  
#define __DQ_IBIT__ 0  
#define __ELF__ 1  
#define __FINITE_MATH_ONLY__ 0  
#define __FLOAT_WORD_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __FLT_DECIMAL_DIG__ 9  
#define __FLT_DENORM_MIN__ 1.40129846432481707092372958328991613e-45F  
#define __FLT_DIG__ 6  
#define __FLT_EPSILON__ 1.19209289550781250000000000000000000e-7F  
#define __FLT_EVAL_METHOD__ 0  
#define __FLT_EVAL_METHOD_TS_18661_3__ 0  
#define __FLT_HAS_DENORM__ 1  
#define __FLT_HAS_INFINITY__ 1  
#define __FLT_HAS_QUIET_NAN__ 1  
#define __FLT_MANT_DIG__ 24  
#define __FLT_MAX__ 3.40282346638528859811704183484516925e+38F  
#define __FLT_MAX_10_EXP__ 38  
#define __FLT_MAX_EXP__ 128  
#define __FLT_MIN__ 1.17549435082228750796873653722224568e-38F  
#define __FLT_MIN_10_EXP__ (-37)  
#define __FLT_MIN_EXP__ (-125)  
#define __FLT_NORM_MAX__ 3.40282346638528859811704183484516925e+38F  
#define __FLT_RADIX__ 2  
#define __FLT128_DECIMAL_DIG__ 36  
#define __FLT128_DENORM_MIN__ 6.47517511943802511092443895822764655e-4966F128  
#define __FLT128_DIG__ 33  
#define __FLT128_EPSILON__ 1.92592994438723585305597794258492732e-34F128  
#define __FLT128_HAS_DENORM__ 1  
#define __FLT128_HAS_INFINITY__ 1  
#define __FLT128_HAS_QUIET_NAN__ 1  
#define __FLT128_MANT_DIG__ 113  
#define __FLT128_MAX__ 1.18973149535723176508575932662800702e+4932F128  
#define __FLT128_MAX_10_EXP__ 4932  
#define __FLT128_MAX_EXP__ 16384  
#define __FLT128_MIN__ 3.36210314311209350626267781732175260e-4932F128  
#define __FLT128_MIN_10_EXP__ (-4931)  
#define __FLT128_MIN_EXP__ (-16381)  
#define __FLT128_NORM_MAX__ 1.18973149535723176508575932662800702e+4932F128  
#define __FLT32_DECIMAL_DIG__ 9  
#define __FLT32_DENORM_MIN__ 1.40129846432481707092372958328991613e-45F32  
#define __FLT32_DIG__ 6  
#define __FLT32_EPSILON__ 1.19209289550781250000000000000000000e-7F32  
#define __FLT32_HAS_DENORM__ 1  
#define __FLT32_HAS_INFINITY__ 1  
#define __FLT32_HAS_QUIET_NAN__ 1  
#define __FLT32_MANT_DIG__ 24  
#define __FLT32_MAX__ 3.40282346638528859811704183484516925e+38F32  
#define __FLT32_MAX_10_EXP__ 38  
#define __FLT32_MAX_EXP__ 128  
#define __FLT32_MIN__ 1.17549435082228750796873653722224568e-38F32  
#define __FLT32_MIN_10_EXP__ (-37)  
#define __FLT32_MIN_EXP__ (-125)  
#define __FLT32_NORM_MAX__ 3.40282346638528859811704183484516925e+38F32  
#define __FLT32X_DECIMAL_DIG__ 17  
#define __FLT32X_DENORM_MIN__ 4.94065645841246544176568792868221372e-324F32x  
#define __FLT32X_DIG__ 15  
#define __FLT32X_EPSILON__ 2.22044604925031308084726333618164062e-16F32x  
#define __FLT32X_HAS_DENORM__ 1  
#define __FLT32X_HAS_INFINITY__ 1  
#define __FLT32X_HAS_QUIET_NAN__ 1  
#define __FLT32X_MANT_DIG__ 53  
#define __FLT32X_MAX__ 1.79769313486231570814527423731704357e+308F32x  
#define __FLT32X_MAX_10_EXP__ 308  
#define __FLT32X_MAX_EXP__ 1024  
#define __FLT32X_MIN__ 2.22507385850720138309023271733240406e-308F32x  
#define __FLT32X_MIN_10_EXP__ (-307)  
#define __FLT32X_MIN_EXP__ (-1021)  
#define __FLT32X_NORM_MAX__ 1.79769313486231570814527423731704357e+308F32x  
#define __FLT64_DECIMAL_DIG__ 17  
#define __FLT64_DENORM_MIN__ 4.94065645841246544176568792868221372e-324F64  
#define __FLT64_DIG__ 15  
#define __FLT64_EPSILON__ 2.22044604925031308084726333618164062e-16F64  
#define __FLT64_HAS_DENORM__ 1  
#define __FLT64_HAS_INFINITY__ 1  
#define __FLT64_HAS_QUIET_NAN__ 1  
#define __FLT64_MANT_DIG__ 53  
#define __FLT64_MAX__ 1.79769313486231570814527423731704357e+308F64  
#define __FLT64_MAX_10_EXP__ 308  
#define __FLT64_MAX_EXP__ 1024  
#define __FLT64_MIN__ 2.22507385850720138309023271733240406e-308F64  
#define __FLT64_MIN_10_EXP__ (-307)  
#define __FLT64_MIN_EXP__ (-1021)  
#define __FLT64_NORM_MAX__ 1.79769313486231570814527423731704357e+308F64  
#define __FLT64X_DECIMAL_DIG__ 36  
#define __FLT64X_DENORM_MIN__ 6.47517511943802511092443895822764655e-4966F64x  
#define __FLT64X_DIG__ 33  
#define __FLT64X_EPSILON__ 1.92592994438723585305597794258492732e-34F64x  
#define __FLT64X_HAS_DENORM__ 1  
#define __FLT64X_HAS_INFINITY__ 1  
#define __FLT64X_HAS_QUIET_NAN__ 1  
#define __FLT64X_MANT_DIG__ 113  
#define __FLT64X_MAX__ 1.18973149535723176508575932662800702e+4932F64x  
#define __FLT64X_MAX_10_EXP__ 4932  
#define __FLT64X_MAX_EXP__ 16384  
#define __FLT64X_MIN__ 3.36210314311209350626267781732175260e-4932F64x  
#define __FLT64X_MIN_10_EXP__ (-4931)  
#define __FLT64X_MIN_EXP__ (-16381)  
#define __FLT64X_NORM_MAX__ 1.18973149535723176508575932662800702e+4932F64x  
#define __FRACT_EPSILON__ 0x1P-15R  
#define __FRACT_FBIT__ 15  
#define __FRACT_IBIT__ 0  
#define __FRACT_MAX__ 0X7FFFP-15R  
#define __FRACT_MIN__ (-0.5R-0.5R)  
#define __GCC_ATOMIC_BOOL_LOCK_FREE 2  
#define __GCC_ATOMIC_CHAR_LOCK_FREE 2  
#define __GCC_ATOMIC_CHAR16_T_LOCK_FREE 2  
#define __GCC_ATOMIC_CHAR32_T_LOCK_FREE 2  
#define __GCC_ATOMIC_INT_LOCK_FREE 2  
#define __GCC_ATOMIC_LLONG_LOCK_FREE 2  
#define __GCC_ATOMIC_LONG_LOCK_FREE 2  
#define __GCC_ATOMIC_POINTER_LOCK_FREE 2  
#define __GCC_ATOMIC_SHORT_LOCK_FREE 2  
#define __GCC_ATOMIC_TEST_AND_SET_TRUEVAL 1  
#define __GCC_ATOMIC_WCHAR_T_LOCK_FREE 2  
#define __GCC_HAVE_BUILTIN_MIPS_CACHE 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_1 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_2 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_4 1  
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_8 1  
#define __GCC_IEC_559 1  
#define __GCC_IEC_559_COMPLEX 1  
#define __gnu_linux__ 1  
#define __GNUC__ 10  
#define __GNUC_MINOR__ 2  
#define __GNUC_PATCHLEVEL__ 1  
#define __GNUC_STDC_INLINE__ 1  
#define __GXX_ABI_VERSION 1014  
#define __HA_FBIT__ 7  
#define __HA_IBIT__ 8  
#define __HQ_FBIT__ 15  
#define __HQ_IBIT__ 0  
#define __INT_FAST16_MAX__ 0x7fffffffffffffffL  
#define __INT_FAST16_TYPE__ long int  
#define __INT_FAST16_WIDTH__ 64  
#define __INT_FAST32_MAX__ 0x7fffffffffffffffL  
#define __INT_FAST32_TYPE__ long int  
#define __INT_FAST32_WIDTH__ 64  
#define __INT_FAST64_MAX__ 0x7fffffffffffffffL  
#define __INT_FAST64_TYPE__ long int  
#define __INT_FAST64_WIDTH__ 64  
#define __INT_FAST8_MAX__ 0x7f  
#define __INT_FAST8_TYPE__ signed char  
#define __INT_FAST8_WIDTH__ 8  
#define __INT_LEAST16_MAX__ 0x7fff  
#define __INT_LEAST16_TYPE__ short int  
#define __INT_LEAST16_WIDTH__ 16  
#define __INT_LEAST32_MAX__ 0x7fffffff  
#define __INT_LEAST32_TYPE__ int  
#define __INT_LEAST32_WIDTH__ 32  
#define __INT_LEAST64_MAX__ 0x7fffffffffffffffL  
#define __INT_LEAST64_TYPE__ long int  
#define __INT_LEAST64_WIDTH__ 64  
#define __INT_LEAST8_MAX__ 0x7f  
#define __INT_LEAST8_TYPE__ signed char  
#define __INT_LEAST8_WIDTH__ 8  
#define __INT_MAX__ 0x7fffffff  
#define __INT_WIDTH__ 32  
#define __INT16_C(c) c  
#define __INT16_MAX__ 0x7fff  
#define __INT16_TYPE__ short int  
#define __INT32_C(c) c  
#define __INT32_MAX__ 0x7fffffff  
#define __INT32_TYPE__ int  
#define __INT64_C(c) c ## L  
#define __INT64_MAX__ 0x7fffffffffffffffL  
#define __INT64_TYPE__ long int  
#define __INT8_C(c) c  
#define __INT8_MAX__ 0x7f  
#define __INT8_TYPE__ signed char  
#define __INTMAX_C(c) c ## L  
#define __INTMAX_MAX__ 0x7fffffffffffffffL  
#define __INTMAX_TYPE__ long int  
#define __INTMAX_WIDTH__ 64  
#define __INTPTR_MAX__ 0x7fffffffffffffffL  
#define __INTPTR_TYPE__ long int  
#define __INTPTR_WIDTH__ 64  
#define __LACCUM_EPSILON__ 0x1P-31LK  
#define __LACCUM_FBIT__ 31  
#define __LACCUM_IBIT__ 32  
#define __LACCUM_MAX__ 0X7FFFFFFFFFFFFFFFP-31LK  
#define __LACCUM_MIN__ (-0X1P31LK-0X1P31LK)  
#define __LANGUAGE_C 1  
#define __LANGUAGE_C__ 1  
#define __LDBL_DECIMAL_DIG__ 36  
#define __LDBL_DENORM_MIN__ 6.47517511943802511092443895822764655e-4966L  
#define __LDBL_DIG__ 33  
#define __LDBL_EPSILON__ 1.92592994438723585305597794258492732e-34L  
#define __LDBL_HAS_DENORM__ 1  
#define __LDBL_HAS_INFINITY__ 1  
#define __LDBL_HAS_QUIET_NAN__ 1  
#define __LDBL_MANT_DIG__ 113  
#define __LDBL_MAX__ 1.18973149535723176508575932662800702e+4932L  
#define __LDBL_MAX_10_EXP__ 4932  
#define __LDBL_MAX_EXP__ 16384  
#define __LDBL_MIN__ 3.36210314311209350626267781732175260e-4932L  
#define __LDBL_MIN_10_EXP__ (-4931)  
#define __LDBL_MIN_EXP__ (-16381)  
#define __LDBL_NORM_MAX__ 1.18973149535723176508575932662800702e+4932L  
#define __LFRACT_EPSILON__ 0x1P-31LR  
#define __LFRACT_FBIT__ 31  
#define __LFRACT_IBIT__ 0  
#define __LFRACT_MAX__ 0X7FFFFFFFP-31LR  
#define __LFRACT_MIN__ (-0.5LR-0.5LR)  
#define __linux 1  
#define __linux__ 1  
#define __LLACCUM_EPSILON__ 0x1P-63LLK  
#define __LLACCUM_FBIT__ 63  
#define __LLACCUM_IBIT__ 64  
#define __LLACCUM_MAX__ 0X7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFP-63LLK  
#define __LLACCUM_MIN__ (-0X1P63LLK-0X1P63LLK)  
#define __LLFRACT_EPSILON__ 0x1P-63LLR  
#define __LLFRACT_FBIT__ 63  
#define __LLFRACT_IBIT__ 0  
#define __LLFRACT_MAX__ 0X7FFFFFFFFFFFFFFFP-63LLR  
#define __LLFRACT_MIN__ (-0.5LLR-0.5LLR)  
#define __LONG_LONG_MAX__ 0x7fffffffffffffffLL  
#define __LONG_LONG_WIDTH__ 64  
#define __LONG_MAX__ 0x7fffffffffffffffL  
#define __LONG_WIDTH__ 64  
#define __LP64__ 1  
#define __mips 64  
#define __mips__ 1  
#define __mips_abicalls 1  
#define __mips_fpr 64  
#define __mips_hard_float 1  
#define __mips_isa_rev 2  
#define __mips_no_madd4 1  
#define __mips64 1  
#define __MIPSEL 1  
#define __MIPSEL__ 1  
#define __NO_INLINE__ 1  
#define __ORDER_BIG_ENDIAN__ 4321  
#define __ORDER_LITTLE_ENDIAN__ 1234  
#define __ORDER_PDP_ENDIAN__ 3412  
#define __pic__ 1  
#define __PIC__ 1  
#define __pie__ 2  
#define __PIE__ 2  
#define __PRAGMA_REDEFINE_EXTNAME 1  
#define __PTRDIFF_MAX__ 0x7fffffffffffffffL  
#define __PTRDIFF_TYPE__ long int  
#define __PTRDIFF_WIDTH__ 64  
#define __QQ_FBIT__ 7  
#define __QQ_IBIT__ 0  
#define __R4000 1  
#define __R4000__ 1  
#define __REGISTER_PREFIX__ $  
#define __SA_FBIT__ 15  
#define __SA_IBIT__ 16  
#define __SACCUM_EPSILON__ 0x1P-7HK  
#define __SACCUM_FBIT__ 7  
#define __SACCUM_IBIT__ 8  
#define __SACCUM_MAX__ 0X7FFFP-7HK  
#define __SACCUM_MIN__ (-0X1P7HK-0X1P7HK)  
#define __SCHAR_MAX__ 0x7f  
#define __SCHAR_WIDTH__ 8  
#define __SFRACT_EPSILON__ 0x1P-7HR  
#define __SFRACT_FBIT__ 7  
#define __SFRACT_IBIT__ 0  
#define __SFRACT_MAX__ 0X7FP-7HR  
#define __SFRACT_MIN__ (-0.5HR-0.5HR)  
#define __SHRT_MAX__ 0x7fff  
#define __SHRT_WIDTH__ 16  
#define __SIG_ATOMIC_MAX__ 0x7fffffff  
#define __SIG_ATOMIC_MIN__ (-__SIG_ATOMIC_MAX__ - 1)  
#define __SIG_ATOMIC_TYPE__ int  
#define __SIG_ATOMIC_WIDTH__ 32  
#define __SIZE_MAX__ 0xffffffffffffffffUL  
#define __SIZE_TYPE__ long unsigned int  
#define __SIZE_WIDTH__ 64  
#define __SIZEOF_DOUBLE__ 8  
#define __SIZEOF_FLOAT__ 4  
#define __SIZEOF_INT__ 4  
#define __SIZEOF_INT128__ 16  
#define __SIZEOF_LONG__ 8  
#define __SIZEOF_LONG_DOUBLE__ 16  
#define __SIZEOF_LONG_LONG__ 8  
#define __SIZEOF_POINTER__ 8  
#define __SIZEOF_PTRDIFF_T__ 8  
#define __SIZEOF_SHORT__ 2  
#define __SIZEOF_SIZE_T__ 8  
#define __SIZEOF_WCHAR_T__ 4  
#define __SIZEOF_WINT_T__ 4  
#define __SQ_FBIT__ 31  
#define __SQ_IBIT__ 0  
#define __STDC__ 1  
#define __STDC_HOSTED__ 1  
#define __STDC_IEC_559__ 1  
#define __STDC_IEC_559_COMPLEX__ 1  
#define __STDC_ISO_10646__ 201706L  
#define __STDC_UTF_16__ 1  
#define __STDC_UTF_32__ 1  
#define __STDC_VERSION__ 201710L  
#define __TA_FBIT__ 63  
#define __TA_IBIT__ 64  
#define __TQ_FBIT__ 127  
#define __TQ_IBIT__ 0  
#define __UACCUM_EPSILON__ 0x1P-16UK  
#define __UACCUM_FBIT__ 16  
#define __UACCUM_IBIT__ 16  
#define __UACCUM_MAX__ 0XFFFFFFFFP-16UK  
#define __UACCUM_MIN__ 0.0UK  
#define __UDA_FBIT__ 32  
#define __UDA_IBIT__ 32  
#define __UDQ_FBIT__ 64  
#define __UDQ_IBIT__ 0  
#define __UFRACT_EPSILON__ 0x1P-16UR  
#define __UFRACT_FBIT__ 16  
#define __UFRACT_IBIT__ 0  
#define __UFRACT_MAX__ 0XFFFFP-16UR  
#define __UFRACT_MIN__ 0.0UR  
#define __UHA_FBIT__ 8  
#define __UHA_IBIT__ 8  
#define __UHQ_FBIT__ 16  
#define __UHQ_IBIT__ 0  
#define __UINT_FAST16_MAX__ 0xffffffffffffffffUL  
#define __UINT_FAST16_TYPE__ long unsigned int  
#define __UINT_FAST32_MAX__ 0xffffffffffffffffUL  
#define __UINT_FAST32_TYPE__ long unsigned int  
#define __UINT_FAST64_MAX__ 0xffffffffffffffffUL  
#define __UINT_FAST64_TYPE__ long unsigned int  
#define __UINT_FAST8_MAX__ 0xff  
#define __UINT_FAST8_TYPE__ unsigned char  
#define __UINT_LEAST16_MAX__ 0xffff  
#define __UINT_LEAST16_TYPE__ short unsigned int  
#define __UINT_LEAST32_MAX__ 0xffffffffU  
#define __UINT_LEAST32_TYPE__ unsigned int  
#define __UINT_LEAST64_MAX__ 0xffffffffffffffffUL  
#define __UINT_LEAST64_TYPE__ long unsigned int  
#define __UINT_LEAST8_MAX__ 0xff  
#define __UINT_LEAST8_TYPE__ unsigned char  
#define __UINT16_C(c) c  
#define __UINT16_MAX__ 0xffff  
#define __UINT16_TYPE__ short unsigned int  
#define __UINT32_C(c) c ## U  
#define __UINT32_MAX__ 0xffffffffU  
#define __UINT32_TYPE__ unsigned int  
#define __UINT64_C(c) c ## UL  
#define __UINT64_MAX__ 0xffffffffffffffffUL  
#define __UINT64_TYPE__ long unsigned int  
#define __UINT8_C(c) c  
#define __UINT8_MAX__ 0xff  
#define __UINT8_TYPE__ unsigned char  
#define __UINTMAX_C(c) c ## UL  
#define __UINTMAX_MAX__ 0xffffffffffffffffUL  
#define __UINTMAX_TYPE__ long unsigned int  
#define __UINTPTR_MAX__ 0xffffffffffffffffUL  
#define __UINTPTR_TYPE__ long unsigned int  
#define __ULACCUM_EPSILON__ 0x1P-32ULK  
#define __ULACCUM_FBIT__ 32  
#define __ULACCUM_IBIT__ 32  
#define __ULACCUM_MAX__ 0XFFFFFFFFFFFFFFFFP-32ULK  
#define __ULACCUM_MIN__ 0.0ULK  
#define __ULFRACT_EPSILON__ 0x1P-32ULR  
#define __ULFRACT_FBIT__ 32  
#define __ULFRACT_IBIT__ 0  
#define __ULFRACT_MAX__ 0XFFFFFFFFP-32ULR  
#define __ULFRACT_MIN__ 0.0ULR  
#define __ULLACCUM_EPSILON__ 0x1P-64ULLK  
#define __ULLACCUM_FBIT__ 64  
#define __ULLACCUM_IBIT__ 64  
#define __ULLACCUM_MAX__ 0XFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFP-64ULLK  
#define __ULLACCUM_MIN__ 0.0ULLK  
#define __ULLFRACT_EPSILON__ 0x1P-64ULLR  
#define __ULLFRACT_FBIT__ 64  
#define __ULLFRACT_IBIT__ 0  
#define __ULLFRACT_MAX__ 0XFFFFFFFFFFFFFFFFP-64ULLR  
#define __ULLFRACT_MIN__ 0.0ULLR  
#define __unix 1  
#define __unix__ 1  
#define __UQQ_FBIT__ 8  
#define __UQQ_IBIT__ 0  
#define __USA_FBIT__ 16  
#define __USA_IBIT__ 16  
#define __USACCUM_EPSILON__ 0x1P-8UHK  
#define __USACCUM_FBIT__ 8  
#define __USACCUM_IBIT__ 8  
#define __USACCUM_MAX__ 0XFFFFP-8UHK  
#define __USACCUM_MIN__ 0.0UHK  
#define __USER_LABEL_PREFIX__   
#define __USFRACT_EPSILON__ 0x1P-8UHR  
#define __USFRACT_FBIT__ 8  
#define __USFRACT_IBIT__ 0  
#define __USFRACT_MAX__ 0XFFP-8UHR  
#define __USFRACT_MIN__ 0.0UHR  
#define __USQ_FBIT__ 32  
#define __USQ_IBIT__ 0  
#define __UTA_FBIT__ 64  
#define __UTA_IBIT__ 64  
#define __UTQ_FBIT__ 128  
#define __UTQ_IBIT__ 0  
#define __VERSION__ "10.2.1 20210110"  
#define __WCHAR_MAX__ 0x7fffffff  
#define __WCHAR_MIN__ (-__WCHAR_MAX__ - 1)  
#define __WCHAR_TYPE__ int  
#define __WCHAR_WIDTH__ 32  
#define __WINT_MAX__ 0xffffffffU  
#define __WINT_MIN__ 0U  
#define __WINT_TYPE__ unsigned int  
#define __WINT_WIDTH__ 32  
#define _ABI64 3  
#define _LANGUAGE_C 1  
#define _LP64 1  
#define _mips 1  
#define _MIPS_ARCH "mips64r2"  
#define _MIPS_ARCH_MIPS64R2 1  
#define _MIPS_FPSET 32  
#define _MIPS_ISA _MIPS_ISA_MIPS64  
#define _MIPS_SIM _ABI64  
#define _MIPS_SPFPSET 32  
#define _MIPS_SZINT 32  
#define _MIPS_SZLONG 64  
#define _MIPS_SZPTR 64  
#define _MIPS_TUNE "mips64r2"  
#define _MIPS_TUNE_MIPS64R2 1  
#define _MIPSEL 1  
#define _R4000 1  
#define _STDC_PREDEF_H 1  
#define BOOST_ARCH_ALPHA BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_ALPHA_NAME "DEC Alpha"  
#define BOOST_ARCH_ARM BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_ARM_NAME "ARM"  
#define BOOST_ARCH_BLACKFIN BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_BLACKFIN_NAME "Blackfin"  
#define BOOST_ARCH_CONVEX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_CONVEX_NAME "Convex Computer"  
#define BOOST_ARCH_E2K BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_E2K_NAME "E2K"  
#define BOOST_ARCH_IA64 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_IA64_NAME "Intel Itanium 64"  
#define BOOST_ARCH_LOONGARCH BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_LOONGARCH_NAME "LoongArch"  
#define BOOST_ARCH_M68K BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_M68K_NAME "Motorola 68k"  
#define BOOST_ARCH_MIPS BOOST_VERSION_NUMBER(__mips,0,0)  
#define BOOST_ARCH_MIPS_AVAILABLE   
#define BOOST_ARCH_MIPS_NAME "MIPS"  
#define BOOST_ARCH_PARISC BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PARISC_NAME "HP/PA RISC"  
#define BOOST_ARCH_PPC BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PPC_64 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PPC_64_NAME "PowerPC64"  
#define BOOST_ARCH_PPC_NAME "PowerPC"  
#define BOOST_ARCH_PTX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PTX_NAME "PTX"  
#define BOOST_ARCH_PWR BOOST_ARCH_RS6000  
#define BOOST_ARCH_PWR_NAME BOOST_ARCH_RS6000_NAME  
#define BOOST_ARCH_PYRAMID BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_PYRAMID_NAME "Pyramid 9810"  
#define BOOST_ARCH_RISCV BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_RISCV_NAME "RISC-V"  
#define BOOST_ARCH_RS6000 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_RS6000_NAME "RS/6000"  
#define BOOST_ARCH_SH BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_SH_NAME "SuperH"  
#define BOOST_ARCH_SPARC BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_SPARC_NAME "SPARC"  
#define BOOST_ARCH_SYS370 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_SYS370_NAME "System/370"  
#define BOOST_ARCH_SYS390 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_SYS390_NAME "System/390"  
#define BOOST_ARCH_WORD_BITS 64  
#define BOOST_ARCH_WORD_BITS_16 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_WORD_BITS_16_NAME "16-bit Word Size"  
#define BOOST_ARCH_WORD_BITS_32 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ARCH_WORD_BITS_32_NAME "32-bit Word Size"  
#define BOOST_ARCH_WORD_BITS_64 BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_ARCH_WORD_BITS_64_NAME "64-bit Word Size"  
#define BOOST_ARCH_WORD_BITS_NAME "Word Bits"  
#define BOOST_ARCH_X86 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_X86_32 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_X86_32_NAME "Intel x86-32"  
#define BOOST_ARCH_X86_64 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_X86_64_NAME "Intel x86-64"  
#define BOOST_ARCH_X86_NAME "Intel x86"  
#define BOOST_ARCH_Z BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_ARCH_Z_NAME "z/Architecture"  
#define BOOST_CONFIG_HELPER_MACROS_HPP_INCLUDED   
#define BOOST_DO_JOIN(X,Y) BOOST_DO_JOIN2(X,Y)  
#define BOOST_DO_JOIN2(X,Y) X ##Y  
#define BOOST_DO_STRINGIZE(X) #X  
#define BOOST_JOIN(X,Y) BOOST_DO_JOIN(X, Y)  
#define BOOST_OS_AIX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_AIX_NAME "IBM AIX"  
#define BOOST_OS_AMIGAOS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_AMIGAOS_NAME "AmigaOS"  
#define BOOST_OS_BEOS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BEOS_NAME "BeOS"  
#define BOOST_OS_BSD BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_BSDI BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_BSDI_NAME "BSDi BSD/OS"  
#define BOOST_OS_BSD_DRAGONFLY BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_DRAGONFLY_NAME "DragonFly BSD"  
#define BOOST_OS_BSD_FREE BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_FREE_NAME "Free BSD"  
#define BOOST_OS_BSD_NAME "BSD"  
#define BOOST_OS_BSD_NET BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_NET_NAME "NetBSD"  
#define BOOST_OS_BSD_OPEN BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_BSD_OPEN_NAME "OpenBSD"  
#define BOOST_OS_CYGWIN BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_CYGWIN_NAME "Cygwin"  
#define BOOST_OS_HAIKU BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_HAIKU_NAME "Haiku"  
#define BOOST_OS_HPUX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_HPUX_NAME "HP-UX"  
#define BOOST_OS_IOS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_IOS_NAME "iOS"  
#define BOOST_OS_IRIX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_IRIX_NAME "IRIX"  
#define BOOST_OS_LINUX BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_OS_LINUX_AVAILABLE   
#define BOOST_OS_LINUX_NAME "Linux"  
#define BOOST_OS_MACOS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_MACOS_NAME "Mac OS"  
#define BOOST_OS_OS400 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_OS400_NAME "IBM OS/400"  
#define BOOST_OS_QNX BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_QNX_NAME "QNX"  
#define BOOST_OS_SOLARIS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_SOLARIS_NAME "Solaris"  
#define BOOST_OS_SVR4 BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_SVR4_NAME "SVR4 Environment"  
#define BOOST_OS_UNIX BOOST_VERSION_NUMBER_AVAILABLE  
#define BOOST_OS_UNIX_AVAILABLE   
#define BOOST_OS_UNIX_NAME "Unix Environment"  
#define BOOST_OS_VMS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_VMS_NAME "VMS"  
#define BOOST_OS_WINDOWS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
#define BOOST_OS_WINDOWS_NAME "Microsoft Windows"  
#define BOOST_PREDEF_ARCHITECTURE_ALPHA_H   
#define BOOST_PREDEF_ARCHITECTURE_ARM_H   
#define BOOST_PREDEF_ARCHITECTURE_BLACKFIN_H   
#define BOOST_PREDEF_ARCHITECTURE_CONVEX_H   
#define BOOST_PREDEF_ARCHITECTURE_E2K_H   
#define BOOST_PREDEF_ARCHITECTURE_H   
#define BOOST_PREDEF_ARCHITECTURE_IA64_H   
#define BOOST_PREDEF_ARCHITECTURE_LOONGARCH_H   
#define BOOST_PREDEF_ARCHITECTURE_M68K_H   
#define BOOST_PREDEF_ARCHITECTURE_MIPS_H   
#define BOOST_PREDEF_ARCHITECTURE_PARISC_H   
#define BOOST_PREDEF_ARCHITECTURE_PPC_H   
#define BOOST_PREDEF_ARCHITECTURE_PTX_H   
#define BOOST_PREDEF_ARCHITECTURE_PYRAMID_H   
#define BOOST_PREDEF_ARCHITECTURE_RISCV_H   
#define BOOST_PREDEF_ARCHITECTURE_RS6K_H   
#define BOOST_PREDEF_ARCHITECTURE_SPARC_H   
#define BOOST_PREDEF_ARCHITECTURE_SUPERH_H   
#define BOOST_PREDEF_ARCHITECTURE_SYS370_H   
#define BOOST_PREDEF_ARCHITECTURE_SYS390_H   
#define BOOST_PREDEF_ARCHITECTURE_X86_32_H   
#define BOOST_PREDEF_ARCHITECTURE_X86_64_H   
#define BOOST_PREDEF_ARCHITECTURE_X86_H   
#define BOOST_PREDEF_ARCHITECTURE_Z_H   
#define BOOST_PREDEF_DECLARE_TEST(x,s)   
#define BOOST_PREDEF_DETAIL_OS_DETECTED 1  
#define BOOST_PREDEF_DETAIL_TEST_H   
#define BOOST_PREDEF_MAKE_0X_VRP(V) BOOST_VERSION_NUMBER((V&0xF00)>>8,(V&0xF0)>>4,(V&0xF))  
#define BOOST_PREDEF_MAKE_0X_VRPP(V) BOOST_VERSION_NUMBER((V&0xF000)>>12,(V&0xF00)>>8,(V&0xFF))  
#define BOOST_PREDEF_MAKE_0X_VRRPP000(V) BOOST_VERSION_NUMBER((V&0xF0000000)>>28,(V&0xFF00000)>>20,(V&0xFF000)>>12)  
#define BOOST_PREDEF_MAKE_0X_VRRPPPP(V) BOOST_VERSION_NUMBER((V&0xF000000)>>24,(V&0xFF0000)>>16,(V&0xFFFF))  
#define BOOST_PREDEF_MAKE_0X_VVRP(V) BOOST_VERSION_NUMBER((V&0xFF00)>>8,(V&0xF0)>>4,(V&0xF))  
#define BOOST_PREDEF_MAKE_0X_VVRR(V) BOOST_VERSION_NUMBER((V&0xFF00)>>8,(V&0xFF),0)  
#define BOOST_PREDEF_MAKE_0X_VVRRP(V) BOOST_VERSION_NUMBER((V&0xFF000)>>12,(V&0xFF0)>>4,(V&0xF))  
#define BOOST_PREDEF_MAKE_0X_VVRRPP(V) BOOST_VERSION_NUMBER((V&0xFF0000)>>16,(V&0xFF00)>>8,(V&0xFF))  
#define BOOST_PREDEF_MAKE_10_VPPP(V) BOOST_VERSION_NUMBER(((V)/1000)%10,0,(V)%1000)  
#define BOOST_PREDEF_MAKE_10_VR0(V) BOOST_VERSION_NUMBER(((V)/100)%10,((V)/10)%10,0)  
#define BOOST_PREDEF_MAKE_10_VRP(V) BOOST_VERSION_NUMBER(((V)/100)%10,((V)/10)%10,(V)%10)  
#define BOOST_PREDEF_MAKE_10_VRP000(V) BOOST_VERSION_NUMBER(((V)/100000)%10,((V)/10000)%10,((V)/1000)%10)  
#define BOOST_PREDEF_MAKE_10_VRPP(V) BOOST_VERSION_NUMBER(((V)/1000)%10,((V)/100)%10,(V)%100)  
#define BOOST_PREDEF_MAKE_10_VRPPPP(V) BOOST_VERSION_NUMBER(((V)/100000)%10,((V)/10000)%10,(V)%10000)  
#define BOOST_PREDEF_MAKE_10_VRR(V) BOOST_VERSION_NUMBER(((V)/100)%10,(V)%100,0)  
#define BOOST_PREDEF_MAKE_10_VRR000(V) BOOST_VERSION_NUMBER(((V)/100000)%10,((V)/1000)%100,0)  
#define BOOST_PREDEF_MAKE_10_VRRPP(V) BOOST_VERSION_NUMBER(((V)/10000)%10,((V)/100)%100,(V)%100)  
#define BOOST_PREDEF_MAKE_10_VV00(V) BOOST_VERSION_NUMBER(((V)/100)%100,0,0)  
#define BOOST_PREDEF_MAKE_10_VVPPP(V) BOOST_VERSION_NUMBER(((V)/1000)%100,0,(V)%1000)  
#define BOOST_PREDEF_MAKE_10_VVRR(V) BOOST_VERSION_NUMBER(((V)/100)%100,(V)%100,0)  
#define BOOST_PREDEF_MAKE_10_VVRR00PP00(V) BOOST_VERSION_NUMBER(((V)/100000000)%100,((V)/1000000)%100,((V)/100)%100)  
#define BOOST_PREDEF_MAKE_10_VVRR0PP00(V) BOOST_VERSION_NUMBER(((V)/10000000)%100,((V)/100000)%100,((V)/100)%100)  
#define BOOST_PREDEF_MAKE_10_VVRR0PPPP(V) BOOST_VERSION_NUMBER(((V)/10000000)%100,((V)/100000)%100,(V)%10000)  
#define BOOST_PREDEF_MAKE_10_VVRRP(V) BOOST_VERSION_NUMBER(((V)/1000)%100,((V)/10)%100,(V)%10)  
#define BOOST_PREDEF_MAKE_10_VVRRPP(V) BOOST_VERSION_NUMBER(((V)/10000)%100,((V)/100)%100,(V)%100)  
#define BOOST_PREDEF_MAKE_10_VVRRPPP(V) BOOST_VERSION_NUMBER(((V)/100000)%100,((V)/1000)%100,(V)%1000)  
#define BOOST_PREDEF_MAKE_DATE(Y,M,D) BOOST_VERSION_NUMBER((Y)%10000-1970,(M)%100,(D)%100)  
#define BOOST_PREDEF_MAKE_H   
#define BOOST_PREDEF_MAKE_YYYY(V) BOOST_PREDEF_MAKE_DATE(V,1,1)  
#define BOOST_PREDEF_MAKE_YYYYMM(V) BOOST_PREDEF_MAKE_DATE((V)/100,(V)%100,1)  
#define BOOST_PREDEF_MAKE_YYYYMMDD(V) BOOST_PREDEF_MAKE_DATE(((V)/10000)%10000,((V)/100)%100,(V)%100)  
#define BOOST_PREDEF_OS_AIX_H   
#define BOOST_PREDEF_OS_AMIGAOS_H   
#define BOOST_PREDEF_OS_BEOS_H   
#define BOOST_PREDEF_OS_BSD_BSDI_H   
#define BOOST_PREDEF_OS_BSD_DRAGONFLY_H   
#define BOOST_PREDEF_OS_BSD_FREE_H   
#define BOOST_PREDEF_OS_BSD_H   
#define BOOST_PREDEF_OS_BSD_NET_H   
#define BOOST_PREDEF_OS_BSD_OPEN_H   
#define BOOST_PREDEF_OS_CYGWIN_H   
#define BOOST_PREDEF_OS_H   
#define BOOST_PREDEF_OS_HAIKU_H   
#define BOOST_PREDEF_OS_HPUX_H   
#define BOOST_PREDEF_OS_IOS_H   
#define BOOST_PREDEF_OS_IRIX_H   
#define BOOST_PREDEF_OS_LINUX_H   
#define BOOST_PREDEF_OS_MACOS_H   
#define BOOST_PREDEF_OS_OS400_H   
#define BOOST_PREDEF_OS_QNXNTO_H   
#define BOOST_PREDEF_OS_SOLARIS_H   
#define BOOST_PREDEF_OS_UNIX_H   
#define BOOST_PREDEF_OS_VMS_H   
#define BOOST_PREDEF_OTHER_WORD_SIZE_H   
#define BOOST_PREDEF_VERSION_NUMBER_H   
#define BOOST_STRINGIZE(X) BOOST_DO_STRINGIZE(X)  
#define BOOST_VERSION_NUMBER(major,minor,patch) ( (((major)%100)*10000000) + (((minor)%100)*100000) + ((patch)%100000) )  
#define BOOST_VERSION_NUMBER_AVAILABLE BOOST_VERSION_NUMBER_MIN  
#define BOOST_VERSION_NUMBER_MAJOR(N) ( ((N)/10000000)%100 )  
#define BOOST_VERSION_NUMBER_MAX BOOST_VERSION_NUMBER(99,99,99999)  
#define BOOST_VERSION_NUMBER_MIN BOOST_VERSION_NUMBER(0,0,1)  
#define BOOST_VERSION_NUMBER_MINOR(N) ( ((N)/100000)%100 )  
#define BOOST_VERSION_NUMBER_NOT_AVAILABLE BOOST_VERSION_NUMBER_ZERO  
#define BOOST_VERSION_NUMBER_PATCH(N) ( (N)%100000 )  
#define BOOST_VERSION_NUMBER_ZERO BOOST_VERSION_NUMBER(0,0,0)  
#define LANGUAGE_C 1  
#define linux 1  
#define mips 1  
#define MIPSEL 1  
#define R4000 1  
#define unix 1  
#define BOOST_PREDEF_OS_WINDOWS_H   
```  
  
See how Clang also defines __mips to either 32 or 64 https://github.com/llvm/llvm-project/blob/8c10256734cd47274671fcabe94f24f15ecd6209/clang/lib/Basic/Targets/Mips.cpp#L86-L94
