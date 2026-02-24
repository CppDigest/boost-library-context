# #114 - Cannot build with clang-11 and libc++ in C++20 mode. [Closed]

> Username: qis  
> Created at: 2020-04-08 01:03:33 UTC  
> Updated at: 2020-04-08 15:40:35 UTC  
> Closed at: 2020-04-08 07:55:39 UTC  
> Url: https://github.com/boostorg/log/issues/114  

This component fails to compile due to not using `std::allocator_traits` with clang-11 and libc++ in C++20 mode.  
  
```  
libs/log/src/attribute_set_impl.hpp:66:33: error: no type named 'size_type' in 'std::__1::allocator<boost::log::v2s_mt_posix::attribute_set::node>'  
    typedef typename base_type::size_type size_type;  
            ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~  
```  
  
* [build log](https://github.com/boostorg/log/files/4447642/build-log.txt)  
* [config.jam](https://github.com/boostorg/log/files/4447646/config.jam.txt)  
  
Command used to build boost:  
  
```  
b2 --layout=system  
   --project-config=/mnt/c/Workspace/vcpkg/buildtrees/boost/x64-linux-dbg/config.jam  
   --prefix=/mnt/c/Workspace/vcpkg/packages/boost_x64-linux/debug  
   --libdir=/mnt/c/Workspace/vcpkg/packages/boost_x64-linux/debug/lib  
   --exec-prefix=/mnt/c/Workspace/vcpkg/packages/boost_x64-linux/debug/bin  
   --includedir=/mnt/c/Workspace/vcpkg/packages/boost_x64-linux/debug/../include  
   --cmakedir=/mnt/c/Workspace/vcpkg/packages/boost_x64-linux/debug/share/boost  
   --build-dir=/mnt/c/Workspace/vcpkg/buildtrees/boost/x64-linux-dbg/build  
   --without-graph_parallel --without-mpi --without-python  
   --includedir=/mnt/c/Workspace/vcpkg/packages/boost_x64-linux/include  
   architecture=x86 address-model=64  
   variant=debug threading=multi  
   link=static runtime-link=shared  
   debug-symbols=on install  
```  
  
The `wave` component has a similar issue: https://github.com/boostorg/wave/issues/84

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-08 07:55:39 UTC  
> Url: https://github.com/boostorg/log/issues/114#issuecomment-610809991  

Already fixed for 1.73.

---

## Comment 2

> Username: qis  
> Created at: 2020-04-08 15:40:35 UTC  
> Url: https://github.com/boostorg/log/issues/114#issuecomment-611032826  

Tested it with the `develop` branch and it seems to work. Sorry for opening the issue and thank you very much!
