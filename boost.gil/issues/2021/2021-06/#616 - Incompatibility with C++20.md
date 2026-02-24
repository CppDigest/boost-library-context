# #616 - Incompatibility with C++20 [Closed]

> Username: fmorgner  
> Created at: 2021-06-10 20:22:27 UTC  
> Updated at: 2022-01-07 20:07:54 UTC  
> Closed at: 2021-10-05 07:31:01 UTC  
> Url: https://github.com/boostorg/gil/issues/616  

### Actual behavior  
  
Compilation fails when using the jpeg io extension (and others).  
  
```  
In file included from /home/sophia/.conan/data/boost/1.76.0/_/_/package/dc8aedd23a0f0a773a5fcdcfe1ae3e89c4205978/include/boost/gil/extension/io/jpeg/read.hpp:13,  
                 from /home/sophia/.conan/data/boost/1.76.0/_/_/package/dc8aedd23a0f0a773a5fcdcfe1ae3e89c4205978/include/boost/gil/extension/io/jpeg.hpp:11,  
                 from /home/sophia/Projects/system/wanda/src/wanda/wallpaper.cpp:8:  
/home/sophia/.conan/data/boost/1.76.0/_/_/package/dc8aedd23a0f0a773a5fcdcfe1ae3e89c4205978/include/boost/gil/extension/io/jpeg/tags.hpp:144:35: error: expected unqualified-id before ‘)’ token  
  144 |     image_read_settings<jpeg_tag>()  
      |                                   ^  
make[2]: *** [CMakeFiles/wandad.dir/build.make:118: CMakeFiles/wandad.dir/src/wanda/wallpaper.cpp.o] Error 1  
make[1]: *** [CMakeFiles/Makefile2:170: CMakeFiles/wandad.dir/all] Error 2  
make: *** [Makefile:146: all] Error 2  
```  
  
### Expected  behavior  
  
Compilation should succeed.  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <boost/gil/extension/io/jpeg.hpp>  
int main() {}  
```  
  
### Environment  
  
With the release of ISO14882:2020, *simple-template-id*s are no longer considered valid as the *id-expression* of the *ptr-declarator* when declaring the constructor of a class template specialization.  
  
See also:  
* DR 2237 @ http://www.open-std.org/jtc1/sc22/wg21/docs/cwg_defects.html  
* https://gcc.gnu.org/bugzilla/show_bug.cgi?id=100997  
* https://gcc.gnu.org/bugzilla/show_bug.cgi?id=97202  
* https://gcc.gnu.org/bugzilla/show_bug.cgi?id=97202  
  
- Compiler version: GCC 11.1  
- Build settings: -std=c++20  
- Version (Git ref or `<boost/version.hpp>`): 1.76.0
