# #711 - color_convert_hsl.cpp and color_convert_rgb.cpp error: no matching constructor for initialization of std::vector<color_t> [Closed]

> Username: mloskot  
> Created at: 2022-07-23 12:56:19 UTC  
> Updated at: 2022-07-23 18:21:29 UTC  
> Closed at: 2022-07-23 18:21:29 UTC  
> Url: https://github.com/boostorg/gil/issues/711  

### Actual behavior  
  
... for `std::vector<color_t> aka `vector<tuple<float, float, float, unsigned char, unsigned char, unsigned char>> `.  
  
https://www.boost.org/development/tests/develop/developer/output/teeks99-dkr-dc3-9-14-boost-bin-v2-libs-gil-test-extension-toolbox-color_convert_hsl-test-clang-linux-3-9~c++14-debug-debug-symbols-off-threading-multi-visibility-hidden.html  
  
```  
Compile [2022-07-16 23:36:14 UTC]: fail  
  
  "clang++-3.9" -c -x c++ -fvisibility-inlines-hidden -Wno-c99-extensions -std=c++14 -fPIC -m64 -pthread -O0 -fno-inline -Wall -fvisibility=hidden --target=x86_64-pc-linux  -DBOOST_ALL_NO_LIB=1 -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -I".." -I"../libs/gil/test"   -o "/var/boost/run/results/boost/bin.v2/libs/gil/test/extension/toolbox/color_convert_hsl.test/clang-linux-3.9~c++14/debug/debug-symbols-off/threading-multi/visibility-hidden/color_convert_hsl.o" "../libs/gil/test/extension/toolbox/color_convert_hsl.cpp"  
  
In file included from ../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:9:  
In file included from ../boost/gil.hpp:43:  
../boost/gil/extension/rasterization/ellipse.hpp:137:54: warning: suggest braces around initialization of subobject [-Wmissing-braces]  
            std::array<std::ptrdiff_t, 4> co_ords = {center2[0] + pnt[0],  
                                                     ^~~~~~~~~~~~~~~~~~~~  
In file included from ../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:9:  
In file included from ../boost/gil.hpp:52:  
../boost/gil/extension/image_processing/diffusion.hpp:117:13: warning: suggest braces around initialization of subobject [-Wmissing-braces]  
    return {point_t{-1, -1}, point_t{0, -1}, point_t{+1, -1}, point_t{+1, 0},  
            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../libs/gil/test/extension/toolbox/color_convert_hsl.cpp:37:26: error: no matching constructor for initialization of 'std::vector<color_t>' (aka 'vector<tuple<float, float, float, unsigned char, unsigned char, unsigned char> >')  
    std::vector<color_t> colors = {  
                         ^        ~  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:401:9: note: candidate constructor template not viable: requires at most 3 arguments, but 8 were provided  
        vector(_InputIterator __first, _InputIterator __last,  
        ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:289:7: note: candidate constructor not viable: requires at most 3 arguments, but 8 were provided  
      vector(size_type __n, const value_type& __value,  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:277:7: note: candidate constructor not viable: requires at most 2 arguments, but 8 were provided  
      vector(size_type __n, const allocator_type& __a = allocator_type())  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:339:7: note: candidate constructor not viable: requires 2 arguments, but 8 were provided  
      vector(const vector& __x, const allocator_type& __a)  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:348:7: note: candidate constructor not viable: requires 2 arguments, but 8 were provided  
      vector(vector&& __rv, const allocator_type& __m)  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:373:7: note: candidate constructor not viable: requires at most 2 arguments, but 8 were provided  
      vector(initializer_list<value_type> __l,  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:264:7: note: candidate constructor not viable: requires single argument '__a', but 8 arguments were provided  
      vector(const allocator_type& __a) _GLIBCXX_NOEXCEPT  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:318:7: note: candidate constructor not viable: requires single argument '__x', but 8 arguments were provided  
      vector(const vector& __x)  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:335:7: note: candidate constructor not viable: requires single argument '__x', but 8 arguments were provided  
      vector(vector&& __x) noexcept  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_vector.h:253:7: note: candidate constructor not viable: requires 0 arguments, but 8 were provided  
      vector()  
      ^  
2 warnings and 1 error generated.  
```  
  
### Expected  behavior  
  
Compilation pass or skip if not requirement not fullfilled.  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: clang 3.9, 4.0, 5.0, 6.0,   
- Build settings: C++14 and C++17  
- Version (Git ref or `<boost/version.hpp>`): pre-1.80
