# #962 - Unable to find boost atomic library header files//boost-1.84.0 [Open]

> Username: BadganchiP1234  
> Created at: 2024-10-07 14:16:13 UTC  
> Updated at: 2024-10-14 14:00:54 UTC  
> Url: https://github.com/boostorg/boost/issues/962  

**ERROR:**  
Compilation failed Stdout: [ 50%] Building CXX object CMakeFiles/test_atomic.dir/atomicity.cpp.o Stderr: /sdk/sysroots/core2-64-poky-linux/usr/share/doc/boost-atomic/examples**/atomicity.cpp:38:10: fatal error: test_clock.hpp: No such file or directory 38 | #include "test_clock.hpp" | ^~~~~~~~~~~~~~~~ compilation terminated. make[2]: ***** [CMakeFiles/test_atomic.dir/build.make:76: CMakeFiles/test_atomic.dir/atomicity.cpp.o] Error 1 make[1]: *** [CMakeFiles/Makefile2:83: CMakeFiles/test_atomic.dir/all] Error 2 make: *** [Makefile:91: all] Error 2 Returncode: Expected: 0 Actual: 2  
  
Tried adding below path but still failing with same issue. Could you please help here?  
  
include_directories(  
    ${Boost_LIBRARY_DIRS}/atomic/test  
)

---

## Comment 1

> Username: BadganchiP1234  
> Created at: 2024-10-14 06:04:54 UTC  
> Url: https://github.com/boostorg/boost/issues/962#issuecomment-2410069944  

Could you please help here?

---

## Comment 2

> Username: mclow  
> Created at: 2024-10-14 14:00:53 UTC  
> Url: https://github.com/boostorg/boost/issues/962#issuecomment-2411360156  

A lot more people will see your problem if you post it to the boost-users mailing list: https://lists.boost.org/mailman/listinfo.cgi/boost-users
