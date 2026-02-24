# #44 - One test fails to compile after boostorg/coroutine submodule updated from d698d1 to d5e610 [Closed]

> Username: spacelg  
> Created at: 2019-11-28 08:05:51 UTC  
> Updated at: 2019-12-01 19:42:01 UTC  
> Closed at: 2019-12-01 19:42:01 UTC  
> Url: https://github.com/boostorg/coroutine/issues/44  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test fails to compile after boostorg/coroutine submodule updated from d698d1 to d5e610 [boost/coroutine@49c1730](https://github.com/boostorg/boost/commit/49c173083739f845f482286f6d24cb5b201423a3). Could you please take a look?  
  
[log_x64_test_124.log](https://github.com/boostorg/coroutine/files/3900562/log_x64_test_124.log)  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\beast\test  
  
ErrorMessage:  
compile-c-c++ ..\out\x64rel\boost\bin.v2\libs\beast\test\beast\core\basic_stream.test\msvc-14.1\release\threadapi-win32\threading-multi\basic_stream.obj  
basic_stream.cpp  
.\boost/coroutine/asymmetric_coroutine.hpp(2364): error C2039: 'begin': is not a member of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(38): note: see declaration of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(2369): error C2039: 'begin': is not a member of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(38): note: see declaration of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(2374): error C2039: 'end': is not a member of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(38): note: see declaration of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(2379): error C2039: 'end': is not a member of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(38): note: see declaration of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(2384): error C2039: 'begin': is not a member of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(38): note: see declaration of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(2389): error C2039: 'end': is not a member of 'boost'  
.\boost/coroutine/asymmetric_coroutine.hpp(38): note: see declaration of 'boost'  
  
    call "..\out\x64rel\boost\bin.v2\standalone\msvc\msvc-14.1\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"..\out\x64rel\boost\bin.v2\libs\beast\test\beast\core\basic_stream.test\msvc-14.1\release\threadapi-win32\threading-multi\basic_stream.obj.rsp"

---

## Comment 1

> Username: olk  
> Created at: 2019-11-28 08:34:43 UTC  
> Url: https://github.com/boostorg/coroutine/issues/44#issuecomment-559393310  

Maybe it is caused by commit a3e511feb9649871e3a31d426dc8f4b23f395ffd - could you revert it and test it again?

---

## Comment 2

> Username: jbeich  
> Created at: 2019-12-01 18:59:24 UTC  
> Updated at: 2019-12-01 18:59:58 UTC  
> Url: https://github.com/boostorg/coroutine/issues/44#issuecomment-560145210  

Also [affects](http://package22.nyi.freebsd.org/data/113amd64-default-PR241449/2019-11-27_11h48m22s/logs/icinga2-2.11.2.log) icinga2 [via](https://github.com/boostorg/asio/search?q=%22coroutine%2Fall.hpp%22) Boost.Asio. Reverting https://github.com/boostorg/coroutine/commit/a3e511feb964 does help.  
  
```  
$ c++ --version  
FreeBSD clang version 9.0.0 (tags/RELEASE_900/final 372316) (based on LLVM 9.0.0)  
Target: x86_64-unknown-freebsd13.0  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
```c++  
$ echo '#include <boost/coroutine/all.hpp>' | c++ -xc++ - -isystem/usr/local/include  
In file included from <stdin>:1:  
In file included from /usr/local/include/boost/coroutine/all.hpp:11:  
In file included from /usr/local/include/boost/coroutine/coroutine.hpp:10:  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2364:10: error: no member named 'begin' in  
      namespace 'boost'; did you mean simply 'begin'?  
{ return boost::begin( c); }  
         ^~~~~~~  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2363:1: note: 'begin' declared here  
begin( pull_coroutine< R > & c)  
^  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2369:10: error: no member named 'begin' in  
      namespace 'boost'; did you mean simply 'begin'?  
{ return boost::begin( c); }  
         ^~~~~~~  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2368:1: note: 'begin' declared here  
begin( pull_coroutine< R > const& c)  
^  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2374:10: error: no member named 'end' in  
      namespace 'boost'; did you mean simply 'end'?  
{ return boost::end( c); }  
         ^~~~~~~  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2373:1: note: 'end' declared here  
end( pull_coroutine< R > & c)  
^  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2379:10: error: no member named 'end' in  
      namespace 'boost'; did you mean simply 'end'?  
{ return boost::end( c); }  
         ^~~~~~~  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2378:1: note: 'end' declared here  
end( pull_coroutine< R > const& c)  
^  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2384:10: error: no member named 'begin' in  
      namespace 'boost'; did you mean simply 'begin'?  
{ return boost::begin( c); }  
         ^~~~~~~  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2383:1: note: 'begin' declared here  
begin( push_coroutine< R > & c)  
^  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2389:10: error: no member named 'end' in  
      namespace 'boost'; did you mean simply 'end'?  
{ return boost::end( c); }  
         ^~~~~~~  
/usr/local/include/boost/coroutine/asymmetric_coroutine.hpp:2388:1: note: 'end' declared here  
end( push_coroutine< R > & c)  
^  
6 errors generated.  
```

---

## Comment 3

> Username: olk  
> Created at: 2019-12-01 19:42:01 UTC  
> Url: https://github.com/boostorg/coroutine/issues/44#issuecomment-560150259  

reverted from mater and develop - ty
