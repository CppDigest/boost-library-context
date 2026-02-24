# #74 architecture: add support for __ARM_ARCH macro [Merged]

> Username: timblechmann  
> Created at: 2018-03-21 11:54:55 UTC  
> Updated at: 2018-06-12 12:40:30 UTC  
> Merged at: 2018-06-12 12:40:30 UTC  
> Closed at: 2018-06-12 12:40:30 UTC  
> Url: https://github.com/boostorg/predef/pull/74  

gcc-6 defines __ARM_ARCH  
  
for the record: the following macros are defined:  
```  
#define __ARM_SIZEOF_WCHAR_T 4  
#define __ARM_FEATURE_UNALIGNED 1  
#define __ARM_FEATURE_IDIV 1  
#define __ARM_FP 14  
#define __ARM_ALIGN_MAX_STACK_PWR 16  
#define __ARM_SIZEOF_MINIMAL_ENUM 4  
#define __ARM_ALIGN_MAX_PWR 28  
#define __ARM_FP16_FORMAT_IEEE 1  
#define __ARM_FP16_ARGS 1  
#define __ARM_FEATURE_FMA 1  
#define __ARM_64BIT_STATE 1  
#define __ARM_ARCH_PROFILE 65  
#define __ARM_PCS_AAPCS64 1  
#define __ARM_FEATURE_CLZ 1  
#define __ARM_ARCH 8  
#define __ARM_ARCH_8A 1  
#define __ARM_NEON 1  
#define __ARM_FEATURE_NUMERIC_MAXMIN 1  
#define __ARM_ARCH_ISA_A64 1  
#define __aarch64__ 1  
```

---
