# #1061 - Inconsistency in the way promotion to `long double` is handled between x86_64 and other architectures, e.g. aarch64 [Open]

> Username: AWSjswinney  
> Created at: 2025-07-07 15:43:31 UTC  
> Updated at: 2025-07-07 15:43:31 UTC  
> Url: https://github.com/boostorg/boost/issues/1061  

## Description  
  
I want to propose disabling promotion to from `double` to `long double` by default in Boost's math functions due to performance implications and architectural differences between platforms. Users who need higher precision can enable promotion with a policy.  
  
Boost documentation on type promotion: https://live.boost.org/doc/libs/1_44_0/libs/math/doc/sf_and_dist/html/math_toolkit/policy/pol_ref/internal_promotion.html  
  
### Current Behavior  
  
- On x86_64: Promotes to 80-bit extended precision `long double` (15-bit exponent, 63-bit precision)  
- On aarch64: Promotes to 128-bit quadruple precision `long double` (15-bit exponent, 112-bit precision). The ARMv8 ISA does not define a floating point type wider than 64 bits and so GCC uses a soft float implementation for the quadruple precision, 128-bit float.  
  
### Issues with Current Behavior  
  
1. Performance Impact  
   - Significant performance overhead from promotion to `long double`  
   - Larger performance delta between architectures (e.g., x86_64 vs aarch64) compared to standard library implementations  
     
2. Architectural Inconsistency  
   - Different precision levels between platforms (63 bits vs 112 bits)  
   - Potentially unexpected behavior when moving code between architectures  
  
3. Limited Benefit  
   - Numerical differences at these precision levels are typically negligible for most use cases  
   - Most applications that truly need extended precision would explicitly specify it
