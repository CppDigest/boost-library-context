# #345 - RISC-V: warning: fedisableexcept is not implemented and will always fail [Open]

> Username: fanquake  
> Created at: 2022-06-23 12:51:25 UTC  
> Updated at: 2022-06-23 12:51:25 UTC  
> Url: https://github.com/boostorg/test/issues/345  

Cross-compiling a minimal Boost Test example for RISC-V:  
```cpp  
#define BOOST_TEST_MODULE Example Test  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE( test1 )  
{  
    int i = 1;  
    BOOST_CHECK( i*i == 1 );  
}  
```  
  
```bash  
riscv64-linux-gnu-g++-11 --version  
riscv64-linux-gnu-g++-11 (Ubuntu 11.2.0-16ubuntu1) 11.2.0  
  
riscv64-linux-gnu-g++-11 example.cpp  
/usr/lib/gcc-cross/riscv64-linux-gnu/11/../../../../riscv64-linux-gnu/bin/ld: /tmp/ccvQglzN.o: in function `.L0 ':  
riscv_boost_test.cpp:(.text+0xd300): warning: fedisableexcept is not implemented and will always fail  
/usr/lib/gcc-cross/riscv64-linux-gnu/11/../../../../riscv64-linux-gnu/bin/ld: riscv_boost_test.cpp:(.text+0xd2ac): warning: feenableexcept is not implemented and will always fail  
```
