# #100 Add RISC-V endian detection [Merged]

> Username: tpetazzoni  
> Created at: 2019-08-03 13:28:07 UTC  
> Updated at: 2019-08-03 15:25:07 UTC  
> Merged at: 2019-08-03 15:25:07 UTC  
> Closed at: 2019-08-03 15:25:07 UTC  
> Url: https://github.com/boostorg/predef/pull/100  

boost/predef/other/endian.h has two ways of detecting the endianess:  
  
 (1) It includes <endian.h> if BOOST_LIB_C_GNU is defined, and then  
     use __BYTE_ORDER to decide the endianness.  
  
 (2) Otherwise, if (1) was not possible for some reason, it uses  
     architecture defines to decide the endianness.  
  
(1) works perfectly fine with glibc toolchains, because  
BOOST_LIB_C_GNU is defined, but it doesn't work with musl. Due to  
this, <endian.h> is not included, __BYTE_ORDER is not defined, and  
method (1) does not work, causing build failures on musl toolchains  
that don't have explicit handling by architecture name (method 2).  
  
So this commit fixes RISC-V musl build by adding support for the  
__riscv architecture define, to determine that the endianness is  
little endian.  
  
Signed-off-by: Thomas Petazzoni <thomas.petazzoni@bootlin.com>

---
