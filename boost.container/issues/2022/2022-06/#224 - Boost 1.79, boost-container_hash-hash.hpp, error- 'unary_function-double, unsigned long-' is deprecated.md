# #224 - Boost 1.79, boost/container_hash/hash.hpp, error: 'unary_function<double, unsigned long>' is deprecated [Closed]

> Username: joaotavora  
> Created at: 2022-06-28 08:04:48 UTC  
> Updated at: 2022-06-28 13:16:41 UTC  
> Closed at: 2022-06-28 13:16:41 UTC  
> Url: https://github.com/boostorg/container/issues/224  

Hello,  
  
I installed boost 1.79 through Conan:  
  
```  
[requires]  
catch2/2.13.9  
benchmark/1.6.1  
cli11/2.2.0  
boost/1.79.0  
  
[generators]  
cmake  
```  
  
My project wide compilation flags include `-std=c++20 -Wall -Wextra -Werror`.  This apparently turns deprecated function warnings into errors. And somewhere along the line, `unary_function` was deprecated.   
  
Any compilation with these flags which eventually attempts to include `<conan's boost dir>/include/boost/container_hash/hash.hpp` will result in lots of these errors, as the `hash.hpp` file seems to be used by many other files.  
  
Here's the relevant `clang++` invocation:  
```  
/usr/bin/clang++ -DCATCH_CONFIG_CONSOLE_WIDTH=60 -I/home/capitaomorte/.conan/data/catch2/2.13.9/_/_/package/5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9/include -I/home/capitaomorte/.conan/data/benchmark/1.6.1/_/_/package/e7908ec8f712a76891b12ae28cb49b24a1c2df49/include -I/home/capitaomorte/.conan/data/cli11/2.2.0/_/_/package/5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9/include -I/home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include -I/home/capitaomorte/.conan/data/zlib/1.2.12/_/_/package/63c2a85d57849e261f98f935b93ecac31ba71b84/include -I/home/capitaomorte/.conan/data/bzip2/1.0.8/_/_/package/1718811d87d80de7c9031968965934b738936751/include -I/home/capitaomorte/.conan/data/libbacktrace/cci.20210118/_/_/package/63c2a85d57849e261f98f935b93ecac31ba71b84/include -I/home/capitaomorte/Source/Cpp/minns2/./src -g  -Wall -Wextra -Werror -pedantic -fdiagnostics-color=always -DBOOST_STACKTRACE_ADDR2LINE_LOCATION=\"/usr/bin/addr2line\" -DBOOST_STACKTRACE_USE_ADDR2LINE -DBOOST_STACKTRACE_USE_BACKTRACE -DBOOST_STACKTRACE_USE_NOOP -std=gnu++20 -MD -MT CMakeFiles/minns2_tests.dir/test/basic-tests.cpp.o -MF CMakeFiles/minns2_tests.dir/test/basic-tests.cpp.o.d -o CMakeFiles/minns2_tests.dir/test/basic-tests.cpp.o -c /home/capitaomorte/Source/Cpp/minns2/test/basic-tests.cpp  
In file included from /home/capitaomorte/Source/Cpp/minns2/test/basic-tests.cpp:3:  
In file included from /home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include/boost/process/child.hpp:22:  
In file included from /home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include/boost/process/detail/execute_impl.hpp:24:  
In file included from /home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include/boost/process/detail/posix/executor.hpp:14:  
In file included from /home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include/boost/process/error.hpp:34:  
In file included from /home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include/boost/type_index.hpp:29:  
In file included from /home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include/boost/type_index/stl_type_index.hpp:47:  
/home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include/boost/container_hash/hash.hpp:132:33: error: 'unary_function<bool, unsigned long>' is deprecated [-Werror,-Wdeprecated-declarations]  
        struct hash_base : std::unary_function<T, std::size_t> {};  
                                ^  
/home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include/boost/container_hash/hash.hpp:609:5: note: in  
instantiation of template class 'boost::hash_detail::hash_base<bool>' requested here  
    BOOST_HASH_SPECIALIZE(bool)  
    ^  
/home/capitaomorte/.conan/data/boost/1.79.0/_/_/package/b155d835ebf8e431aa2991e51da5d4a8d4f2d499/include/boost/container_hash/hash.hpp:581:19: note: expanded from macro 'BOOST_HASH_SPECIALIZE'  
         : public boost::hash_detail::hash_base<type> \  
                  ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/12.1.0/../../../../include/c++/12.1.0/bits/stl_function.h:124:7: note: 'unary_function<bool, unsigned long>'  
has been explicitly marked deprecated here  
    } _GLIBCXX11_DEPRECATED;  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/12.1.0/../../../../include/c++/12.1.0/x86_64-pc-linux-gnu/bits/c++config.h:103:32: note: expanded from macro  
'_GLIBCXX11_DEPRECATED'  
# define _GLIBCXX11_DEPRECATED _GLIBCXX_DEPRECATED  
                               ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/12.1.0/../../../../include/c++/12.1.0/x86_64-pc-linux-gnu/bits/c++config.h:94:46: note: expanded from macro '_GLIBCXX_DEPRECATED'  
# define _GLIBCXX_DEPRECATED __attribute__ ((__deprecated__))  
```

---

## Comment 1

> Username: joaotavora  
> Created at: 2022-06-28 13:16:41 UTC  
> Url: https://github.com/boostorg/container/issues/224#issuecomment-1168711197  

According to @pdimov, this is a dup of https://github.com/boostorg/container_hash/issues/22, which has a upcoming fix and a workaround.  Closing.
