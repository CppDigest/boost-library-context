# #989 Allow use of ibm128 long doubles [Closed]

> Username: mborland  
> Created at: 2023-06-01 08:20:42 UTC  
> Updated at: 2023-06-06 10:42:16 UTC  
> Closed at: 2023-06-06 10:42:16 UTC  
> Url: https://github.com/boostorg/math/pull/989  

PPC64LE currently defaults to the ibm128 long double type instead of float128. It is not IEEE754 compliant because it pre-dates the addition of binary128, but it should have adequate precision to promote from a double.  
  
See [here](https://reviews.llvm.org/D137513) for macro definitions.  
  
Closes #988   
  
@jzmaddock since `LDBL_MANT_DIG` of ibm128 is 106 did you previously hit issues with it? I see a bunch of old comments, but nothing about the powerPC architectures.

---
