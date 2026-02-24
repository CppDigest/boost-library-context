# #247 - compile failed when including cobalt/io.hpp [Closed]

> Username: zhscn  
> Created at: 2025-09-08 04:01:25 UTC  
> Updated at: 2025-09-11 14:09:11 UTC  
> Closed at: 2025-09-11 14:09:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/247  

```  
/usr/lib64/ccache/c++ -DBOOST_COBALT_DYN_LINK -DBOOST_COBALT_NO_LIB -DBOOST_CONTAINER_DYN_LINK -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTEXT_DYN_LINK -DBOOST_CONTEXT_NO_LIB -DBOOST_DATE_TIME_DYN_LINK -DBOOST_DATE_TIME_NO_LIB -isystem /opt/boost/include -g -std=gnu++23 -MD -MT src/CMakeFiles/io.dir/io.cc.o -MF src/CMakeFiles/io.dir/io.cc.o.d -o src/CMakeFiles/io.dir/io.cc.o -c /home/zhscn/code/foo/src/io.cc  
In file included from /opt/boost/include/boost/cobalt/io.hpp:19,  
                 from /home/zhscn/code/foo/src/io.cc:2:  
/opt/boost/include/boost/cobalt/io/random_access_file.hpp:26:1: error: expected class-name before ‘{’ token  
   26 | {  
      | ^  
```  
  
```cpp  
#include <boost/cobalt.hpp>  
// #include <boost/cobalt/io.hpp>  
#include <boost/cobalt/io/stream.hpp>  
#include <boost/cobalt/io/random_access_file.hpp>  
  
namespace cobalt = boost::cobalt;  
  
cobalt::main co_main(int argc, char* argv[]) {  
  // co_await things here  
  co_return 0;  
}  
```  
  
Boost version: 1.89.0, built with `./b2 cxxstd=20 install --prefix=/opt/boost`  
gcc version 15.2.1 20250808 (Red Hat 15.2.1-1) (GCC)

---

## Comment 1

> Username: zhscn  
> Created at: 2025-09-08 04:03:38 UTC  
> Url: https://github.com/boostorg/cobalt/issues/247#issuecomment-3264507357  

The output of clang 20.1.8:  
```  
In file included from /opt/boost/include/boost/cobalt/io.hpp:19:  
/opt/boost/include/boost/cobalt/io/random_access_file.hpp:25:56: error: unknown class name 'random_access_device'; did you mean 'random_access_file'?  
   25 | struct BOOST_SYMBOL_VISIBLE random_access_file : file, random_access_device  
      |                                                        ^  
/opt/boost/include/boost/cobalt/io/random_access_file.hpp:25:29: note: 'random_access_file' declared here  
   25 | struct BOOST_SYMBOL_VISIBLE random_access_file : file, random_access_device  
      |                             ^  
/opt/boost/include/boost/cobalt/io/random_access_file.hpp:25:56: error: base class has incomplete type  
   25 | struct BOOST_SYMBOL_VISIBLE random_access_file : file, random_access_device  
      |                                                        ^~~~~~~~~~~~~~~~~~~~  
/opt/boost/include/boost/cobalt/io/random_access_file.hpp:25:29: note: definition of 'boost::cobalt::io::random_access_file' is not complete until the closing '}'  
   25 | struct BOOST_SYMBOL_VISIBLE random_access_file : file, random_access_device  
      |                             ^  
2 errors generated.  
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-09-10 17:17:53 UTC  
> Url: https://github.com/boostorg/cobalt/issues/247#issuecomment-3275820358  

That is an unfortunate include guarsld typo. Fix is in master, you shoukd use individual includes until the next boost release.
