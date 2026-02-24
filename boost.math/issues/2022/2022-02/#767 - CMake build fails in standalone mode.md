# #767 - CMake build fails in standalone mode: [Closed]

> Username: NAThompson  
> Created at: 2022-02-27 19:43:20 UTC  
> Updated at: 2022-02-28 17:58:09 UTC  
> Closed at: 2022-02-28 17:58:09 UTC  
> Url: https://github.com/boostorg/math/issues/767  

```  
~/math/build ❯❯❯ cmake ../ -G Ninja                                                                                                           develop ✭ ◼  
-- The CXX compiler identification is AppleClang 13.0.0.13000029  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Boost.Math: standalone mode ON  
-- Configuring done  
-- Generating done  
-- Build files have been written to: ~/math/build  
~/m/build ❯❯❯ ninja                                                                                                                        develop ✭ ◼  
[145/246] Building CXX object test/compile_test/CMakeFiles/boost_math-compile_tests.dir/interpolators_cardinal_trig_incl_test.cpp.o  
FAILED: test/compile_test/CMakeFiles/boost_math-compile_tests.dir/interpolators_cardinal_trig_incl_test.cpp.o  
/Library/Developer/CommandLineTools/usr/bin/c++ -DBOOST_MATH_STANDALONE=1 -I~/math/include -arch arm64 -isysroot /Library/Developer/CommandLineTools/SDKs/MacOSX12.1.sdk -std=gnu++17 -MD -MT test/compile_test/CMakeFiles/boost_math-compile_tests.dir/interpolators_cardinal_trig_incl_test.cpp.o -MF test/compile_test/CMakeFiles/boost_math-compile_tests.dir/interpolators_cardinal_trig_incl_test.cpp.o.d -o test/compile_test/CMakeFiles/boost_math-compile_tests.dir/interpolators_cardinal_trig_incl_test.cpp.o -c ~/math/test/compile_test/interpolators_cardinal_trig_incl_test.cpp  
In file included from ~/math/test/compile_test/interpolators_cardinal_trig_incl_test.cpp:9:  
In file included from ~/math/include/boost/math/interpolators/cardinal_trigonometric.hpp:9:  
~/math/include/boost/math/interpolators/detail/cardinal_trigonometric_detail.hpp:11:10: fatal error: 'fftw3.h' file not found  
#include <fftw3.h>  
         ^~~~~~~~~  
1 error generated.  
[156/246] Building CXX object test/compile_test/CMakeFiles/boost_math-compile_tests.dir/interpolators_cubic_spline_incl_test.cpp.o  
ninja: build stopped: subcommand failed.  
```  
  
Should `fftw3.h` be wrapped in a `__has_include`?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-02-28 09:18:49 UTC  
> Url: https://github.com/boostorg/math/issues/767#issuecomment-1054049110  

I would gut the contents of the _test_ in the event that `__has_Include(<fftw3.h>)` is false: we can't do anything in the header since that dependency is absolutely required to use that component?
