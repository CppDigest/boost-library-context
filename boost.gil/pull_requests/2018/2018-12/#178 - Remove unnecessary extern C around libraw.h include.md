# #178 Remove unnecessary extern C around libraw.h include [Merged]

> Username: mloskot  
> Created at: 2018-12-09 15:15:48 UTC  
> Updated at: 2018-12-09 20:39:41 UTC  
> Merged at: 2018-12-09 20:39:00 UTC  
> Closed at: 2018-12-09 20:39:00 UTC  
> Url: https://github.com/boostorg/gil/pull/178  

Since [libraw](https://www.libraw.org/) is C++ library, not C, it is always built as C++ library.  
  
### Actual Behaviour  
  
The current `#include <libraw/libraw.h>` is this:  
  
```  
#ifndef BOOST_GIL_EXTENSION_IO_RAW_C_LIB_COMPILED_AS_CPLUSPLUS  
    extern "C" {  
#endif  
  
#include <libraw/libraw.h>  
  
#ifndef BOOST_GIL_EXTENSION_IO_RAW_C_LIB_COMPILED_AS_CPLUSPLUS  
    }  
#endif  
```  
  
if compiled without `BOOST_GIL_EXTENSION_IO_RAW_C_LIB_COMPILED_AS_CPLUSPLUS` defined, compiler throws errors:  
  
```  
$ VERBOSE=1 make test_header_extension-io-raw-detail-read  
...  
[100%] Building CXX object test/headers/CMakeFiles/test_header_extension-io-raw-detail-read.dir/extension-io-raw-detail-read.cpp.o  
cd /mnt/d/boost.wsl/libs/gil/_build.gcc5/test/headers && /usr/bin/c++   -I/mnt/d/boost.wsl/libs/gil/include -I/mnt/d/boost.wsl/libs/gil/test -isystem /usr/include/x86_64-linux-gnu -isystem /mnt/d/boost.wsl  -fstrict-aliasing -pedantic -Wall -Wconversion -Wextra -Wfloat-equal -Wshadow -Wsign-promo -Wstrict-aliasing -Wunused-parameter -std=c++11 -o CMakeFiles/test_header_extension-io-raw-detail-read.dir/extension-io-raw-detail-read.cpp.o -c /mnt/d/boost.wsl/libs/gil/_build.gcc5/test/headers/extension-io-raw-detail-read.cpp  
In file included from /usr/include/c++/5/fstream:40:0,  
                 from /usr/include/libraw/libraw_datastream.h:34,  
                 from /usr/include/libraw/libraw.h:36,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/raw/tags.hpp:17,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/raw/detail/read.hpp:11,  
                 from /mnt/d/boost.wsl/libs/gil/_build.gcc5/test/headers/extension-io-raw-detail-read.cpp:14:  
/usr/include/c++/5/bits/codecvt.h:67:3: error: template with C linkage  
   template<typename _InternT, typename _ExternT, typename _StateT>  
   ^  
/usr/include/c++/5/bits/codecvt.h:273:4: error: template with C linkage  
    template<typename _InternT, typename _ExternT, typename _StateT>  
    ^  
/usr/include/c++/5/bits/codecvt.h:333:3: error: template with C linkage  
   template<typename _InternT, typename _ExternT, typename _StateT>  
   ^  
/usr/include/c++/5/bits/codecvt.h:337:3: error: template specialization with C linkage  
   template<>  
   ^  
/usr/include/c++/5/bits/codecvt.h:400:3: error: template specialization with C linkage  
   template<>  
   ^  
/usr/include/c++/5/bits/codecvt.h:467:3: error: template specialization with C linkage  
   template<>  
   ^  
/usr/include/c++/5/bits/codecvt.h:524:3: error: template specialization with C linkage  
   template<>  
   ^  
/usr/include/c++/5/bits/codecvt.h:581:3: error: template with C linkage  
   template<typename _InternT, typename _ExternT, typename _StateT>  
   ^  
/usr/include/c++/5/bits/codecvt.h:609:3: error: template specialization with C linkage  
   template<>  
   ^  
/usr/include/c++/5/bits/codecvt.h:627:3: error: template specialization with C linkage  
   template<>  
   ^  
In file included from /usr/include/c++/5/fstream:42:0,  
                 from /usr/include/libraw/libraw_datastream.h:34,  
                 from /usr/include/libraw/libraw.h:36,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/raw/tags.hpp:17,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/raw/detail/read.hpp:11,  
                 from /mnt/d/boost.wsl/libs/gil/_build.gcc5/test/headers/extension-io-raw-detail-read.cpp:14:  
/usr/include/x86_64-linux-gnu/c++/5/bits/basic_file.h:49:3: error: template with C linkage  
   template<typename _CharT>  
   ^  
/usr/include/x86_64-linux-gnu/c++/5/bits/basic_file.h:53:3: error: template specialization with C linkage  
   template<>  
   ^  
In file included from /usr/include/libraw/libraw_datastream.h:34:0,  
                 from /usr/include/libraw/libraw.h:36,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/raw/tags.hpp:17,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/raw/detail/read.hpp:11,  
                 from /mnt/d/boost.wsl/libs/gil/_build.gcc5/test/headers/extension-io-raw-detail-read.cpp:14:  
/usr/include/c++/5/fstream:71:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/fstream:454:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/fstream:655:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/fstream:861:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/fstream:1050:3: error: template with C linkage  
   template <class _CharT, class _Traits>  
   ^  
/usr/include/c++/5/fstream:1057:3: error: template with C linkage  
   template <class _CharT, class _Traits>  
   ^  
/usr/include/c++/5/fstream:1064:3: error: template with C linkage  
   template <class _CharT, class _Traits>  
   ^  
/usr/include/c++/5/fstream:1071:3: error: template with C linkage  
   template <class _CharT, class _Traits>  
   ^  
In file included from /usr/include/c++/5/fstream:1081:0,  
                 from /usr/include/libraw/libraw_datastream.h:34,  
                 from /usr/include/libraw/libraw.h:36,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/raw/tags.hpp:17,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/raw/detail/read.hpp:11,  
                 from /mnt/d/boost.wsl/libs/gil/_build.gcc5/test/headers/extension-io-raw-detail-read.cpp:14:  
/usr/include/c++/5/bits/fstream.tcc:46:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:60:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:78:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:93:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:121:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:150:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:176:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:210:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:260:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:286:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:445:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:504:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:569:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:632:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:720:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:768:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:797:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:857:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:872:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:898:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:916:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:977:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
/usr/include/c++/5/bits/fstream.tcc:994:3: error: template with C linkage  
   template<typename _CharT, typename _Traits>  
   ^  
```  
  
### Expected Behaviour  
  
Successful compilation.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
