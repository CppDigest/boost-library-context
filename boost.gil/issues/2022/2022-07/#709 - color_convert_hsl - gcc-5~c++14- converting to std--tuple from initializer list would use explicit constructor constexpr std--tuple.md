# #709 - color_convert_hsl / gcc-5~c++14: converting to std::tuple from initializer list would use explicit constructor constexpr std::tuple [Closed]

> Username: mloskot  
> Created at: 2022-07-23 12:46:18 UTC  
> Updated at: 2022-07-23 18:21:30 UTC  
> Closed at: 2022-07-23 18:21:29 UTC  
> Url: https://github.com/boostorg/gil/issues/709  

### Actual behavior  
  
The `/test/extension/toolbox/color_convert_hsl.cpp` is failing on Boost regression build:  
  
https://www.boost.org/development/tests/develop/developer/output/teeks99-dkr-dg5-boost-bin-v2-libs-gil-test-extension-toolbox-color_convert_hsl-test-gcc-5~c++14-debug-debug-symbols-off-threading-multi-visibility-hidden.html  
  
```  
Compile [2022-07-10 16:43:20 UTC]: fail  
  
    "g++-5"   -fvisibility-inlines-hidden -std=c++14 -fPIC -m64 -pthread -O0 -fno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION  -I".." -I"../libs/gil/test"  -c -o "/var/boost/run/results/boost/bin.v2/libs/gil/test/extension/toolbox/color_convert_hsl.test/gcc-5~c++14/debug/debug-symbols-off/threading-multi/visibility-hidden/color_convert_hsl.o" "../libs/gil/test/extension/toolbox/color_convert_hsl.cpp"  
  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp: In function 'void test_colors_hsl_to_rgb()':  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:46:5: error: converting to 'std::tuple<float, float, float, unsigned char, unsigned char, unsigned char>' from initializer list would use explicit constructor 'constexpr std::tuple< <template-parameter-1-1> >::tuple(_UElements&& ...) [with _UElements = {int, int, int, int, int, int}; <template-parameter-2-2> = void; _Elements = {float, float, float, unsigned char, unsigned char, unsigned char}]'  
     };  
     ^  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:46:5: error: converting to 'std::tuple<float, float, float, unsigned char, unsigned char, unsigned char>' from initializer list would use explicit constructor 'constexpr std::tuple< <template-parameter-1-1> >::tuple(_UElements&& ...) [with _UElements = {int, int, int, int, int, int}; <template-parameter-2-2> = void; _Elements = {float, float, float, unsigned char, unsigned char, unsigned char}]'  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:46:5: error: converting to 'std::tuple<float, float, float, unsigned char, unsigned char, unsigned char>' from initializer list would use explicit constructor 'constexpr std::tuple< <template-parameter-1-1> >::tuple(_UElements&& ...) [with _UElements = {int, int, double, int, int, int}; <template-parameter-2-2> = void; _Elements = {float, float, float, unsigned char, unsigned char, unsigned char}]'  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:46:5: error: converting to 'std::tuple<float, float, float, unsigned char, unsigned char, unsigned char>' from initializer list would use explicit constructor 'constexpr std::tuple< <template-parameter-1-1> >::tuple(_UElements&& ...) [with _UElements = {float, int, double, int, int, int}; <template-parameter-2-2> = void; _Elements = {float, float, float, unsigned char, unsigned char, unsigned char}]'  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:46:5: error: converting to 'std::tuple<float, float, float, unsigned char, unsigned char, unsigned char>' from initializer list would use explicit constructor 'constexpr std::tuple< <template-parameter-1-1> >::tuple(_UElements&& ...) [with _UElements = {float, int, double, int, int, int}; <template-parameter-2-2> = void; _Elements = {float, float, float, unsigned char, unsigned char, unsigned char}]'  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:46:5: error: converting to 'std::tuple<float, float, float, unsigned char, unsigned char, unsigned char>' from initializer list would use explicit constructor 'constexpr std::tuple< <template-parameter-1-1> >::tuple(_UElements&& ...) [with _UElements = {float, int, double, int, int, int}; <template-parameter-2-2> = void; _Elements = {float, float, float, unsigned char, unsigned char, unsigned char}]'  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:46:5: error: converting to 'std::tuple<float, float, float, unsigned char, unsigned char, unsigned char>' from initializer list would use explicit constructor 'constexpr std::tuple< <template-parameter-1-1> >::tuple(_UElements&& ...) [with _UElements = {float, int, double, int, int, int}; <template-parameter-2-2> = void; _Elements = {float, float, float, unsigned char, unsigned char, unsigned char}]'  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:46:5: error: converting to 'std::tuple<float, float, float, unsigned char, unsigned char, unsigned char>' from initializer list would use explicit constructor 'constexpr std::tuple< <template-parameter-1-1> >::tuple(_UElements&& ...) [with _UElements = {float, int, double, int, int, int}; <template-parameter-2-2> = void; _Elements = {float, float, float, unsigned char, unsigned char, unsigned char}]'  
```  
  
### Expected  behavior  
  
Compilation pass or skip, if compiler/mode not supported.  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: GCC 5  
- Build settings: C++14  
- Version (Git ref or `<boost/version.hpp>`): pre-1.80, [rev f1bf6e68289b4ad313f6715f0fc1e633b58f4b7f](https://github.com/boostorg/boost/commit/f1bf6e68289b4ad313f6715f0fc1e633b58f4b7f)
