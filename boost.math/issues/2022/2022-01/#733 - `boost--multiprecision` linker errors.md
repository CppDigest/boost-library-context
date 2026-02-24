# #733 - `boost::multiprecision` linker errors [Closed]

> Username: mborland  
> Created at: 2022-01-06 19:48:01 UTC  
> Updated at: 2022-01-08 14:39:06 UTC  
> Closed at: 2022-01-08 14:39:06 UTC  
> Url: https://github.com/boostorg/math/issues/733  

@jzmaddock I am not quite sure if this is from the recent `__float128` merge into multi-precision, but the drone runs are now showing linker errors like the below.  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1.o  
1723s  
2028	gcc.link ../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1  
1724s  
2029	/usr/bin/ld: ../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1.o: in function `boost::multiprecision::float128_procs::ldexp(__float128, int)':  
1724s  
2030	test_tgamma_for_issue396_part1.cpp:(.text._ZN5boost14multiprecision14float128_procs5ldexpEgi[_ZN5boost14multiprecision14float128_procs5ldexpEgi]+0x1e): undefined reference to `ldexpq'  
1724s  
2031	/usr/bin/ld: ../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1.o: in function `boost::multiprecision::float128_procs::frexp(__float128, int*)':  
1724s  
2032	test_tgamma_for_issue396_part1.cpp:(.text._ZN5boost14multiprecision14float128_procs5frexpEgPi[_ZN5boost14multiprecision14float128_procs5frexpEgPi]+0x21): undefined reference to `frexpq'  
1724s  
2033	/usr/bin/ld: ../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1.o: in function `boost::multiprecision::float128_procs::floor(__float128)':  
1724s  
2034	test_tgamma_for_issue396_part1.cpp:(.text._ZN5boost14multiprecision14float128_procs5floorEg[_ZN5boost14multiprecision14float128_procs5floorEg]+0x16): undefined reference to `floorq'  
1724s  
2035	/usr/bin/ld: ../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1.o: in function `boost::math::isinf(__float128 const&)':  
1724s  
2036	test_tgamma_for_issue396_part1.cpp:(.text._ZN5boost4math5isinfERKg[_ZN5boost4math5isinfERKg]+0x3b): undefined reference to `isinfq'  
1724s  
2037	/usr/bin/ld: ../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1.o: in function `boost::math::isnan(__float128 const&)':  
1724s  
2038	test_tgamma_for_issue396_part1.cpp:(.text._ZN5boost4math5isnanERKg[_ZN5boost4math5isnanERKg]+0x3b): undefined reference to `isnanq'  
1724s  
2039	collect2: error: ld returned 1 exit status  
1724s  
2040	  
1729s  
2041	    "g++-10"    -o "../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1" -Wl,--start-group "../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1.o" "../../../bin.v2/libs/test/build/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/libboost_unit_test_framework.a"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -m64 -pthread -fvisibility=hidden -fvisibility-inlines-hidden -fsanitize=address   
1729s  
2042	  
1729s  
2043	...failed gcc.link ../../../bin.v2/libs/math/test/test_tgamma_for_issue396_part1.test/gcc-10/debug/debug-symbols-off/link-static/threading-multi/visibility-hidden/test_tgamma_for_issue396_part1...  
```  
  
Complete run can be found [here](https://drone.cpp.al/boostorg/math/516/1/2).

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-01-07 10:48:07 UTC  
> Url: https://github.com/boostorg/math/issues/733#issuecomment-1007312594  

Oh :(  
  
And yes it's the recent change: the effect of the changes is that all the multiprecision types are now constructible from a __float128, and the result of that, is that lower precision types will use __float128 literals for constants in boost/math/constants/constants.hpp.  I guess in practice, relatively few people will be hit by that, as most folks won't be using cpp_dec_float at < 30 digit precision.  But it is annoying :(  
  
So... I guess we just need to add a conditional link to libquadmath on that test case.
