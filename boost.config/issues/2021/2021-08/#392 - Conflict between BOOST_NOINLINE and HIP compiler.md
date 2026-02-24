# #392 - Conflict between BOOST_NOINLINE and HIP compiler [Closed]

> Username: mdewing  
> Created at: 2021-08-06 15:56:08 UTC  
> Updated at: 2022-04-29 18:38:11 UTC  
> Closed at: 2021-08-09 18:07:38 UTC  
> Url: https://github.com/boostorg/config/issues/392  

The hipcc compiler (tested on rocm 4.3) defines `__noinline__` as `__atribute__((noinline))` (in /opt/rocm-4.3.0/hip/include/hip/amd_detail/host_defines.h)  
This definition mixes with the boost definition to create an incorrect `__attribute__` statement  
Shows up when including stacktrace.hpp  
  
  
Reproducer  
```  
// Workaround  
//#define BOOST_NOINLINE __attribute__((noinline))__  
  
#define BOOST_STACKTRACE_USE_BACKTRACE  
#include <stacktrace.hpp>  
  
#include <hip/hip_runtime.h>  
  
int main()  
{  
    return 0;  
}  
  
BOOST_NOINLINE void stuff()  
{  
    int i = 1;  
}  
```  
  
Compile with hipcc and get the following output  
```  
boost_test.cpp:13:1: error: use of undeclared identifier 'noinline'; did you mean 'inline'?  
BOOST_NOINLINE void stuff()  
^  
/usr/include/boost/config/detail/suffix.hpp:623:46: note: expanded from macro 'BOOST_NOINLINE'  
#      define BOOST_NOINLINE __attribute__ ((__noinline__))  
                                             ^  
/opt/rocm-4.3.0/hip/include/hip/amd_detail/host_defines.h:50:37: note: expanded from macro '__noinline__'  
#define __noinline__ __attribute__((noinline))  
                                    ^  
boost_test.cpp:13:1: error: type name does not allow function specifier to be specified  
/usr/include/boost/config/detail/suffix.hpp:623:46: note: expanded from macro 'BOOST_NOINLINE'  
#      define BOOST_NOINLINE __attribute__ ((__noinline__))  
                                             ^  
/opt/rocm-4.3.0/hip/include/hip/amd_detail/host_defines.h:50:37: note: expanded from macro '__noinline__'  
#define __noinline__ __attribute__((noinline))  
                                    ^  
boost_test.cpp:13:1: error: expected expression  
/usr/include/boost/config/detail/suffix.hpp:623:46: note: expanded from macro 'BOOST_NOINLINE'  
#      define BOOST_NOINLINE __attribute__ ((__noinline__))  
                                             ^  
/opt/rocm-4.3.0/hip/include/hip/amd_detail/host_defines.h:50:46: note: expanded from macro '__noinline__'  
#define __noinline__ __attribute__((noinline))  
                                             ^  
3 errors generated when compiling for gfx900.  
```  
  
The fix in boost is likely to be a check similar to the check for `__CUDACC__` already in the BOOST_NOINLINE definition.  I don't know the correct preprocessor symbol to use for checking for HIP compilation.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-08-08 10:55:15 UTC  
> Url: https://github.com/boostorg/config/issues/392#issuecomment-894779172  

Can you please verify that https://github.com/boostorg/config/pull/393 fixes this for you?

---

## Comment 2

> Username: mdewing  
> Created at: 2021-08-09 16:00:12 UTC  
> Url: https://github.com/boostorg/config/issues/392#issuecomment-895342988  

Yes, #393 fixes this issue for me.

---

## Comment 3

> Username: atamazov  
> Created at: 2022-04-26 14:38:56 UTC  
> Url: https://github.com/boostorg/config/issues/392#issuecomment-1109880057  

@markdewing   
>  I don't know the correct preprocessor symbol to use for checking for HIP compilation.  
  
Please look at https://github.com/ROCm-Developer-Tools/HIP/blob/develop/docs/markdown/hip_porting_guide.md#compiler-defines-summary. `__HIP__` is defined for HIP-Clang only and should work fine in this case.

---

## Comment 4

> Username: atamazov  
> Created at: 2022-04-26 14:46:30 UTC  
> Updated at: 2022-04-26 15:03:58 UTC  
> Url: https://github.com/boostorg/config/issues/392#issuecomment-1109889533  

@jzmaddock @markdewing `HIP_VERSION` is not defined in the HIP-clang but defined in HIP headers. Therefore the usage of `HIP_VERSION` creates dependence on the order of inclusion of BOOST and HIP headers (for example, please see https://github.com/ROCmSoftwarePlatform/MIOpen/pull/1490#issue-1186353579). I highly recommend reopening the issue and re-implementing the fix (please see previous comment for advice).

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-04-29 17:22:31 UTC  
> Url: https://github.com/boostorg/config/issues/392#issuecomment-1113548917  

So is simply changing `HIP_VERSION` to `__HIP__` the correct fix?

---

## Comment 6

> Username: atamazov  
> Created at: 2022-04-29 18:38:11 UTC  
> Url: https://github.com/boostorg/config/issues/392#issuecomment-1113608536  

@jzmaddock Yes. If there is any doubt, then I think we can ask @markdewing to check if this works ;)
