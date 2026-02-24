# #70 - declaration of `bad_archive_exception` not available [Closed]

> Username: jkhsjdhjs  
> Created at: 2023-09-09 11:54:29 UTC  
> Updated at: 2023-09-09 15:15:50 UTC  
> Closed at: 2023-09-09 15:15:50 UTC  
> Url: https://github.com/boostorg/multi_index/issues/70  

I'm trying to build p4c, the reference compiler for the P4 language. This worked fine with Boost 1.81, but fails with 1.83 due to the following error:  
```  
In file included from /usr/include/boost/multi_index/hashed_index.hpp:32,  
                 from /usr/include/boost/graph/named_graph.hpp:18,  
                 from /usr/include/boost/graph/adjacency_list.hpp:36,  
                 from /home/jkhsjdhjs/aur/p4lang-p4c/src/p4c-1.2.4.3/backends/graphs/graphs.h:33,  
                 from /home/jkhsjdhjs/aur/p4lang-p4c/src/p4c-1.2.4.3/backends/graphs/graphs.cpp:17,  
                 from /home/jkhsjdhjs/aur/p4lang-p4c/src/p4c-1.2.4.3/build/backends/graphs/CMakeFiles/p4cgraphs.dir/Unity/unity_0_cxx.cxx:4:  
/usr/include/boost/multi_index/detail/bucket_array.hpp: In function ‘void boost::serialization::load_construct_data(Archive&, boost::multi_index::detail::bucket_array<Allocator>*, unsigned int)’:  
/usr/include/boost/multi_index/detail/bucket_array.hpp:239:19: error: there are no arguments to ‘bad_archive_exception’ that depend on a template parameter, so a declaration of ‘bad_archive_exception’ must be available [-fpermissive]  
  239 |   throw_exception(bad_archive_exception());  
      |                   ^~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/multi_index/detail/bucket_array.hpp:239:19: note: (if you use ‘-fpermissive’, G++ will accept your code, but allowing the use of an undeclared name is deprecated)  
In file included from /usr/include/boost/multi_index/hashed_index.hpp:35:  
/usr/include/boost/multi_index/detail/index_node_base.hpp: In function ‘void boost::serialization::load_construct_data(Archive&, boost::multi_index::detail::index_node_base<Value, Allocator>*, unsigned int)’:  
/usr/include/boost/multi_index/detail/index_node_base.hpp:120:19: error: there are no arguments to ‘bad_archive_exception’ that depend on a template parameter, so a declaration of ‘bad_archive_exception’ must be available [-fpermissive]  
  120 |   throw_exception(bad_archive_exception());  
      |                   ^~~~~~~~~~~~~~~~~~~~~  
```  
  
Looking at the trace, this seems to be a boost issue. What do you think?  
  
See also: https://github.com/p4lang/p4c/issues/4147

---

## Comment 1

> Username: joaquintides  
> Created at: 2023-09-09 14:22:26 UTC  
> Url: https://github.com/boostorg/multi_index/issues/70#issuecomment-1712523197  

Hi, this is indeed a bug on Boost.MultiIndex. Can you provide information as to what compiler was used?

---

## Comment 2

> Username: jkhsjdhjs  
> Created at: 2023-09-09 14:39:44 UTC  
> Url: https://github.com/boostorg/multi_index/issues/70#issuecomment-1712527261  

Of course, I'm using gcc 13.2.1 on Arch Linux:  
```  
$ c++ -v  
Using built-in specs.  
COLLECT_GCC=c++  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-pc-linux-gnu/13.2.1/lto-wrapper  
Target: x86_64-pc-linux-gnu  
Configured with: /build/gcc/src/gcc/configure --enable-languages=ada,c,c++,d,fortran,go,lto,objc,obj-c++ --enable-bootstrap --prefix=/usr --libdir=/usr/lib --libexecdir=/usr/lib --mandir=/usr/share/man --infodir=/usr/share/info --with-bugurl=https://bugs.archlinux.org/ --with-build-config=bootstrap-lto --with-linker-hash-style=gnu --with-system-zlib --enable-__cxa_atexit --enable-cet=auto --enable-checking=release --enable-clocale=gnu --enable-default-pie --enable-default-ssp --enable-gnu-indirect-function --enable-gnu-unique-object --enable-libstdcxx-backtrace --enable-link-serialization=1 --enable-linker-build-id --enable-lto --enable-multilib --enable-plugin --enable-shared --enable-threads=posix --disable-libssp --disable-libstdcxx-pch --disable-werror  
Thread model: posix  
Supported LTO compression algorithms: zlib zstd  
gcc version 13.2.1 20230801 (GCC)  
```  
  
The ran command is:  
  
```  
[ 92%] Building CXX object backends/graphs/CMakeFiles/p4cgraphs.dir/Unity/unity_0_cxx.cxx.o  
cd /home/jkhsjdhjs/aur/p4lang-p4c/src/p4c-1.2.4.3/build/backends/graphs && /usr/bin/c++ -DBOOST_NO_ARGUMENT_DEPENDENT_LOOKUP -DCONFIG_PKGDATADIR=\"/usr/share/p4c\" -DCONFIG_PREFIX=\"/usr\" -DMAX_LOGGING_LEVEL=10 -DP4C_GTEST_ENABLED -I/home/jkhsjdhjs/aur/p4lang-p4c/src/p4c-1.2.4.3/extensions -I/home/jkhsjdhjs/aur/p4lang-p4c/src/p4c-1.2.4.3 -I/home/jkhsjdhjs/aur/p4lang-p4c/src/p4c-1.2.4.3/build -isystem /home/jkhsjdhjs/aur/p4lang-p4c/src/googletest-1.14.0/googlemock/include -isystem /home/jkhsjdhjs/aur/p4lang-p4c/src/googletest-1.14.0/googletest/include -march=x86-64 -mtune=generic -O2 -pipe -fno-plt -fexceptions         -Wp,-D_FORTIFY_SOURCE=2 -Wformat -Werror=format-security         -fstack-clash-protection -fcf-protection -Wp,-D_GLIBCXX_ASSERTIONS -fdiagnostics-color=never  -Wall -Wextra -Wno-overloaded-virtual -Wno-deprecated -Wno-deprecated-declarations -pedantic -std=gnu++17 -fPIC -MD -MT backends/graphs/CMakeFiles/p4cgraphs.dir/Unity/unity_0_cxx.cxx.o -MF CMakeFiles/p4cgraphs.dir/Unity/unity_0_cxx.cxx.o.d -o CMakeFiles/p4cgraphs.dir/Unity/unity_0_cxx.cxx.o -c /home/jkhsjdhjs/aur/p4lang-p4c/src/p4c-1.2.4.3/build/backends/graphs/CMakeFiles/p4cgraphs.dir/Unity/unity_0_cxx.cxx  
```

---

## Comment 3

> Username: joaquintides  
> Created at: 2023-09-09 14:41:23 UTC  
> Url: https://github.com/boostorg/multi_index/issues/70#issuecomment-1712527649  

Ok, the bug is triggered by `-DBOOST_NO_ARGUMENT_DEPENDENT_LOOKUP`. Is there any reason you're defining this macro?

---

## Comment 4

> Username: jkhsjdhjs  
> Created at: 2023-09-09 15:03:17 UTC  
> Url: https://github.com/boostorg/multi_index/issues/70#issuecomment-1712532204  

It is defined in the CMakeLists.txt of p4c:  
https://github.com/p4lang/p4c/blob/87d362f409685159470628d565b430abe3342300/CMakeLists.txt#L150-L151  
  
Looking at the blame, it has been added 6 years ago. I don't know enough about p4c/boost to understand the comment. @antoninbas: If you're still around, can you comment on this?

---

## Comment 5

> Username: joaquintides  
> Created at: 2023-09-09 15:07:54 UTC  
> Url: https://github.com/boostorg/multi_index/issues/70#issuecomment-1712533068  

Just curious: you're basically declaring that the compiler does not support argument-dependent lookup, which is only true for very very old compilers. In any case, this configuration has revealed a bug in Boost.MultiIndex, so thanks for this.  
  
Can you please try https://github.com/boostorg/multi_index/commit/2b2c52a27f5800c6e5eb566cc330c4bbe2387de6 locally and confirm your problem's solved?

---

## Comment 6

> Username: jkhsjdhjs  
> Created at: 2023-09-09 15:15:50 UTC  
> Url: https://github.com/boostorg/multi_index/issues/70#issuecomment-1712534845  

Thanks for the explanation and the fix! I applied https://github.com/boostorg/multi_index/commit/2b2c52a27f5800c6e5eb566cc330c4bbe2387de6 locally and was able to build p4c just fine, so yes, it works!
