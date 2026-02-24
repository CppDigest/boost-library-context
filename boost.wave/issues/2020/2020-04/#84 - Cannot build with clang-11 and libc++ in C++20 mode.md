# #84 - Cannot build with clang-11 and libc++ in C++20 mode. [Closed]

> Username: qis  
> Created at: 2020-04-08 01:03:36 UTC  
> Updated at: 2020-04-10 15:23:11 UTC  
> Closed at: 2020-04-10 15:23:11 UTC  
> Url: https://github.com/boostorg/wave/issues/84  

This component fails to compile due to not using `std::allocator_traits` with clang-11 and libc++ in C++20 mode.  
  
```  
boost/wave/util/flex_string.hpp:547:25: error: no type named 'size_type' in 'std::__1::allocator<char>'  
    typedef typename A::size_type size_type;  
            ~~~~~~~~~~~~^~~~~~~~~  
```  
  
* [build log](https://github.com/boostorg/wave/files/4447651/build-log.txt)  
* [config.jam](https://github.com/boostorg/wave/files/4447652/config.jam.txt)  
  
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
  
The `log` component has a similar issue: https://github.com/boostorg/log/issues/114

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-04-08 01:11:14 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-610696715  

Thanks for the bug report. We fixed some of these but missed the rest :(

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-04-08 04:25:51 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-610744099  

@qis would you try my branch and see if it fixes your problem? https://github.com/jefftrull/wave/tree/bugfix/allocator-traits

---

## Comment 3

> Username: qis  
> Created at: 2020-04-08 15:51:18 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-611038824  

@jefftrull Thanks! It looks better now, but doesn't compile yet: [install-x64-linux-dbg-out.log](https://github.com/boostorg/wave/files/4451471/install-x64-linux-dbg-out.log)

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-04-08 18:22:27 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-611115602  

Thanks again and sorry to do this iteratively - for some reason my clang doesn't flag these :( I have updated my branch again, can you recheck?

---

## Comment 5

> Username: qis  
> Created at: 2020-04-08 18:57:39 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-611132886  

Awesome! Commit c9d096b fixed the build. If you're interested in warnings, here they are: [install-x64-linux-dbg-out.log](https://github.com/boostorg/wave/files/4452295/install-x64-linux-dbg-out.log)  
  
Could it be that you couldn't reproduce the problems because clang was still using libstdc++? I built LLVM with `-DCLANG_DEFAULT_CXX_STDLIB="libc++"`.  
  
Thank you very much! :)

---

## Comment 6

> Username: mclow  
> Created at: 2020-04-09 13:42:33 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-611534965  

I just ran into this while testing the 1.73.0 beta 1 release candidate.

---

## Comment 7

> Username: mclow  
> Created at: 2020-04-09 13:43:04 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-611535218  

I'll try on `develop` to see

---

## Comment 8

> Username: glenfe  
> Created at: 2020-04-09 13:58:35 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-611542967  

@jefftrull  I left comments on your pull request.

---

## Comment 9

> Username: jefftrull  
> Created at: 2020-04-09 14:34:34 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-611561873  

@mclow is this holding up 1.73? Maybe I should retarget to master as a bug fix?

---

## Comment 10

> Username: mclow  
> Created at: 2020-04-09 17:03:31 UTC  
> Url: https://github.com/boostorg/wave/issues/84#issuecomment-611639800  

> @mclow is this holding up 1.73? Maybe I should retarget to master as a bug fix?  
  
IMHO, this is not worth holding up the beta for. Unreleased compiler, unreleased standard.  
However, I think it *ought to be fixed* for the 1.73 release, which is in a couple weeks.
