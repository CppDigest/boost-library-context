# #90 - Build problem [Closed]

> Username: rodolfo-picoreti  
> Created at: 2016-10-09 14:40:21 UTC  
> Updated at: 2022-03-31 13:40:50 UTC  
> Closed at: 2016-10-09 15:20:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/90  

Hi,   
  
I have built and installed boost 1.62 and I'm trying to build a simple fiber program using the following command:  
  
> clang++ fiber.cpp -std=c++14 -I /usr/local/include/boost/ -l boost_context -l boost_system -l boost_thread -l pthread  
  
But I'm getting undefined references to:  
- boost::fibers::fiber::start_()  
- boost::fibers::context::active()  
- boost::fibers::context::set_ready_(boost::fibers::context*)  
- boost::fibers::context::set_terminated()  
  
The program:  
  
``` cpp  
#include <iostream>  
#include <boost/fiber/all.hpp>  
  
int main(int argc, char const *argv[]) {  
  auto fiber = boost::fibers::fiber([] () {  
    std::cout << "Hello World" << std::endl;  
  });  
  return 0;  
}  
```

---

## Comment 1

> Username: olk  
> Created at: 2016-10-09 15:20:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-252492483  

link against boost.fiber, e.g. add -l boost_fiber

---

## Comment 2

> Username: rodolfo-picoreti  
> Created at: 2016-10-09 15:26:48 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-252492899  

I tried that, but when I compile boost using b2 it outputs:  
  
> - fiber                    : building  
  
However libboost_fiber.so is not being generated in "boost_1_62_0/stage/lib". So I get:  
  
>  /usr/bin/ld: cannot find -lboost_fiber

---

## Comment 3

> Username: olk  
> Created at: 2016-10-09 16:53:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-252497813  

no idea - seams your build process has some problems  
check if some errors pop up if you build: b2 --with-fiber

---

## Comment 4

> Username: xaqq  
> Created at: 2016-11-14 16:16:09 UTC  
> Updated at: 2016-11-14 16:16:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-260380810  

Hello @olk,  
  
I have a problem similar to @rodolfo-picoreti. I've download boost 1.62 from boost.org, and ran the `./b2` script.   
  
I also see `fiber: building` in the b2 output, but the shared library is not built either.

---

## Comment 5

> Username: xaqq  
> Created at: 2016-11-14 17:08:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-260396456  

It turns out the library wasn't built due to incorrect compiler feature detection.  
  
Adding `cxxflags='-std=c++14'` on the `./b2` script invocation fixed the problem.

---

## Comment 6

> Username: Mellnik  
> Created at: 2016-12-09 22:39:18 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-266142864  

This is mine:  
``./b2 cxxflags="-std=c++14" -j 6 threading=multi variant=release link=shared stage``  
fiber is not being built nor does anything pop up in the log.  
What's wrong?  
  
Using GCC 5.4.0

---

## Comment 7

> Username: olk  
> Created at: 2016-12-10 07:49:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-266187922  

maybe yoa habe to apply --reconfigure to b2 -> re-evaluate c++ Features (will be cached)

---

## Comment 8

> Username: Mellnik  
> Created at: 2016-12-10 08:35:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-266190059  

That worked. Thanks!

---

## Comment 9

> Username: terrywh  
> Created at: 2017-07-04 08:45:36 UTC  
> Updated at: 2017-07-04 08:50:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-312819125  

`./b2 cxxflags="-std=c++14" --reconfigure --with-fiber` not working for me (centos 7/gcc 6.3.0/boost 1.64.0), not getting `stage/lib/libboost_fiber.a` or `stage/lib/libbost_fiber.so`. "Feature Check" shows: "cxx11_thread_local : no". Anything else i can do ?

---

## Comment 10

> Username: olk  
> Created at: 2017-07-04 08:52:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-312820854  

'b2 cxxflags="-std=c++14" --reconfigure --with-fiber' works for me -> ./stage/lib contains  libboost_fiber.so and required libraries

---

## Comment 11

> Username: ncook-hxgn  
> Created at: 2021-12-13 15:41:08 UTC  
> Updated at: 2021-12-13 15:55:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-992604594  

Just built 1.78 with the following batch file (Windows 10) to find the same problems as mentioned above: a missing boost_fibre dll where there are libboost_fibre libs.  
  
```bat  
setlocal  
pushd E:\repos\boost\src\boost_1_78_0  
call bootstrap.bat  
b2 headers  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=static runtime-link=shared threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=shared runtime-link=static threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=static runtime-link=static threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=static runtime-link=shared threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=shared runtime-link=static threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=static runtime-link=static threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=static runtime-link=shared threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=shared runtime-link=static threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=static runtime-link=static threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=static runtime-link=shared threading=multi address-model=64 --build-dir="E:\repos\\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=shared runtime-link=static threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 pch=off -j8 architecture=x86 link=static runtime-link=static threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
popd  
endlocal  
```  
  
I will try with this one and see what happens:  
```bat  
setlocal  
pushd E:\repos\boost\src\boost_1_78_0  
call bootstrap.bat  
b2 headers  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=static runtime-link=shared threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=shared runtime-link=static threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=static runtime-link=static threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=static runtime-link=shared threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=shared runtime-link=static threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=static runtime-link=static threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=static runtime-link=shared threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=shared runtime-link=static threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=static runtime-link=static threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=static runtime-link=shared threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=shared runtime-link=static threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 cxxflags="-std=c++14" pch=off -j8 architecture=x86 link=static runtime-link=static threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
popd  
endlocal  
```  
  
> maybe yoa habe to apply --reconfigure to b2 -> re-evaluate c++ Features (will be cached)  
  
@olk do you know where this cache is on disk? I've run into this in a build of a newly extracted [source archive](https://boostorg.jfrog.io/artifactory/main/release/1.78.0/source) - boost_1_78_0.zip

---

## Comment 12

> Username: ncook-hxgn  
> Created at: 2021-12-13 16:00:38 UTC  
> Updated at: 2021-12-13 16:49:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-992622573  

Addendum:  adding the `cxxflags="-std=c++14"` did not make fibre dlls build.  
Edit: that's probably because I'm feeding gcc args to cl #FeelsLikeMonday  
  
I am now trying `--reconfigure --with-fibre`. I wouldn't have thought ` --with-fibre` would be necessary if fibre is reported by b2 to be building, which it is (and I'm getting the libs, just not the dlls).. alas.  
  
FWIW, Boost.Fibre is not present at all in the boost_1_78_0-bin-msvc-all-32-64.7z  binary distribution for 1.78.0 found [here](https://boostorg.jfrog.io/artifactory/main/release/1.78.0/binaries) at all, neither the libs nor the dlls

---

## Comment 13

> Username: ncook-hxgn  
> Created at: 2021-12-13 16:14:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-992635885  

Addendum II:  
  
Can't `--with-fibre` and `--without-python`:  
`EXEC : error : both --with-<library> and --without-<library> specified`  
  
😩

---

## Comment 14

> Username: olk  
> Created at: 2021-12-13 17:37:19 UTC  
> Updated at: 2021-12-13 17:37:54 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-992712452  

I'm not responsible for boostorg.jfrog.io

---

## Comment 15

> Username: olk  
> Created at: 2021-12-13 17:37:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-992712754  

Note it's named fiber instead of fibre

---

## Comment 16

> Username: ncook-hxgn  
> Created at: 2021-12-13 17:57:49 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-992727989  

@olk thanks for the response :)   
I only mention what's on boostorg.jfrog.io for comparison to the build output I'm getting from the source archive.  
  
If the official binary distribution of boost doesn't have fibre included, possibly the canonical build used to produce that binary distro is broken?  
  
I went back to not bothering with `--with-fiber` (or some poorly-spelled variation of) in my commandline, and the b2 build says it is building fiber:  
  
(You caught me experimenting with working around a warning with ` --allow-shared-static`)  
```bat  
setlocal  
pushd E:\repos\boost\src\boost_1_78_0  
call bootstrap.bat  
b2 headers  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=static runtime-link=shared threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=shared runtime-link=static threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=static runtime-link=static threading=single address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=static runtime-link=shared threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=shared runtime-link=static threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=static runtime-link=static threading=multi address-model=32 --build-dir="E:\repos\boost\obj\32\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=static runtime-link=shared threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=shared runtime-link=static threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=static runtime-link=static threading=single address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=shared runtime-link=shared threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=static runtime-link=shared threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\shared" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=shared runtime-link=static threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
b2 --toolset=msvc-14.2 --allow-shared-static pch=off -j8 architecture=x86 link=static runtime-link=static threading=multi address-model=64 --build-dir="E:\repos\boost\obj\64\static" stage --stagedir="E:\repos\boost\src\boost_1_78_0\stage\vc142" --without-python  
popd  
endlocal  
```  
  
This is how that manifested:  
![image](https://user-images.githubusercontent.com/36889813/145862070-e37eeafd-c28d-4d1c-ae9c-c921f2d8852c.png)  
  
In fact, I am even getting warnings from the fiber build:  
```  
 .\boost/fiber/algo/algorithm.hpp(33): warning C4251: 'boost::fibers::algo::algorithm::use_count_': struct 'std::atomic<unsigned __int64>' needs to have dll-interface to be used by clients of class 'boost::f  
ibers::algo::algorithm' [E:\repos\boost\Boost.proj]  
  .\boost/fiber/waker.hpp(58): warning C4251: 'boost::fibers::waker_with_hook::waker_queue_hook_': class 'boost::intrusive::slist_member_hook<>' needs to have dll-interface to be used by clients of class 'boo  
st::fibers::waker_with_hook' [E:\repos\boost\Boost.proj]  
  .\boost/fiber/waker.hpp(73): warning C4251: 'boost::fibers::wait_queue::slist_': class 'boost::intrusive::slist<boost::fibers::waker_with_hook,boost::intrusive::member_hook<boost::fibers::waker_with_hook,bo  
ost::fibers::detail::waker_queue_hook,pointer-to-member(0x10)>,boost::intrusive::constant_time_size<false>,boost::intrusive::cache_last<true>>' needs to have dll-interface to be used by clients of class 'boos  
t::fibers::wait_queue' [E:\repos\boost\Boost.proj]  
```  
  
Just, no actual libs:  
  
```bat  
E:\repos\boost>dir src\boost_1_78_0\stage\vc142\lib\boost_fiber-vc142-mt-sgd-x64-1_78.lib  
 Volume in drive E is Data  
 Volume Serial Number is CCA5-58EE  
  
 Directory of E:\repos\boost\src\boost_1_78_0\stage\vc142\lib  
  
File Not Found  
  
E:\repos\boost>dir src\boost_1_78_0\stage\vc142\lib\boost_fiber-vc142-mt-sgd-x64-1_78.dll  
 Volume in drive E is Data  
 Volume Serial Number is CCA5-58EE  
  
 Directory of E:\repos\boost\src\boost_1_78_0\stage\vc142\lib  
  
File Not Found  
  
E:\repos\boost>  
```

---

## Comment 17

> Username: ncook-hxgn  
> Created at: 2021-12-14 10:06:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-993375848  

Can somebody help?  
This build process worked with 1.69 and 1.71.. admittedly I've fallen a bit behind.

---

## Comment 18

> Username: iegad  
> Created at: 2022-03-31 13:40:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/90#issuecomment-1084592963  

boost 1.78 on ubuntu 20.04 cannot find boost_fiber  
my way:   
cd boost_1_78_0/lib/fiber  
cmake .  
make   
mv libboost_fiber.a /usr/local/lib  
  
done!!!
