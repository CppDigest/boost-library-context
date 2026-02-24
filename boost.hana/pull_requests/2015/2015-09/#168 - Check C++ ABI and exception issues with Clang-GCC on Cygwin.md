# #168 [Build] Check C++ ABI and exception issues with Clang/GCC on Cygwin [Closed]

> Username: m-j-w  
> Created at: 2015-09-01 00:04:49 UTC  
> Updated at: 2015-09-09 19:18:00 UTC  
> Closed at: 2015-09-08 12:28:03 UTC  
> Url: https://github.com/boostorg/hana/pull/168  

Fixes  #167  
- Check whether compiler supports and successfully compiles with exceptions.  
- Check whether the provided stdlib has C++14 type traits fully implemented.  
- Perform some compiler version checks in cmake and give hints to the user.  
  
Both errors occured on Cygwin using Clang 3.5.2 with libstdc++ from GCC  
4.9.3. However, Clang may also use the GCC stdlib on other systems, like  
Linux Mint.  
The error with exceptions is also expected to occure on MSYS2 and maybe  
Windows, since Clang seems to have general problems there.  
  
Unfortunately, the incomplete stdlib is supposedly not detectable from within  
the compilation. Thus a test is created which may fail during compilation.  
If so, an additional symbol is defined to handle the issue:  
BOOST_HANA_CXX_INCOMPLETE_STDLIB  
  
Availability of exceptions is tested with the existance of __EXCEPTIONS, and  
handled accordingly. Instead of throw, a assert(false) or abort() comes to mind.  
  
Lastly, the cmake cygwin warning message is deactivated in CMakeLists.txt,  
and the main include <boost/hana/hana.hpp> checks the C++ standard to be  
at least C++14 or raises an error otherwise. Should give beginners a valuable  
hint on what is going wrong...  
  
A side note for other Cygwin/MinGW/MSYS2 users: Compile with Clang or  
GCC with static binding of (both) standard libs using   
     -static-libgcc -static-libstdc++  
Makes your life a lot easier.  
  
Remaining issues are some non-compiling tests and examples. These have been  
removed from the build on Cygwin. The reason may also be the incomplete stlib.  
See test/CMakeLists.txt and example/CMakeLists.txt  
  
Tested on  
- Cygwin/Clang 3.5.2/libstdc++ of gcc 4.9.3 w/o exceptions (success)  
- Cygwin/Clang 3.5.2/libstdc++ of gcc 4.9.3 w/ exceptions (failure, but captured)  
- Cygwin/GCC 4.9.3/libstdc++ of gcc 4.9.3 (failure, as expected)  
- Linux Mint/Clang 3.6.0/libstdc++ of gcc 4.8.4 (failure, as expected)  
- Linux Mint/Clang 3.6.0/libc++ of clang 3.6.0 w/ exceptions (success)  
- Linux Mint/gcc 4.8.4/libstdc++ of gcc 4.8.4 (uncaptured failure, as expected)

---

## Comment 1

> Username: ldionne  
> Created_at: 2015-09-01 02:32:22 UTC  
> Updated_at: 2015-09-03 00:21:47 UTC  
> Url: https://github.com/boostorg/hana/pull/168#discussion_r38380816  

You probably want your name there, not mine (x3).

---

## Comment 2

> Username: ldionne  
> Created_at: 2015-09-01 02:47:13 UTC  
> Url: https://github.com/boostorg/hana/pull/168#issuecomment-136558222  

Concerning the build failure: I broke the `develop` branch, and you (correctly) based your pull request on that broken commit. Can you rebase your branch on top of the latest `develop` so the tests pass? Sorry about that, it wasn't my best shot. It's late now; I'll have a look at the PR tomorrow and will leave some comments, but it looks good so far.

---

## Comment 3

> Username: m-j-w  
> Created_at: 2015-09-01 17:23:45 UTC  
> Url: https://github.com/boostorg/hana/pull/168#issuecomment-136802581  

I think I successfully rebased the pull request and added a contribution line. Sorry for the multiple commits, I'll squash it after your final code review. Good looking doesn't mean good behaving...

---

## Comment 4

> Username: ldionne  
> Created_at: 2015-09-02 15:13:39 UTC  
> Url: https://github.com/boostorg/hana/pull/168#issuecomment-137121226  

After looking at this PR quite extensively, here are my thoughts. There are actually three things we're trying to achieve here:  
1. Add more user-friendly diagnostics for supported compilers during the CMake generation step. This is a great idea, and I like your take on it to have a separate file deal with it. One thing that worries me, however, is that some of these checks are already done in [boost/hana/config.hpp](https://github.com/ldionne/hana/blob/0ccb92776c8a488a8085c0a3f4ca4c8d23654ad8/include/boost/hana/config.hpp). I'm slightly worried about duplicating these checks such that they eventually get out of sync. Do you think it is not sufficient to have these checks in `hana/config.hpp`?  
2. Add support for incomplete C++14 standard libraries. I'm not sure about this one. Hana has a very strict policy regarding compiler/stdlib requirements, as documented in the [README](https://github.com/ldionne/hana#hacking-on-hana). I am chilly at the idea of maintaining workarounds for non-C++14 stuff. It simply isn't worth it, since there are literally hundreds of workarounds we could do for specific platforms just to have to library's tests compile, but each of these workarounds makes our life more difficult in the long term (especially since they can't all be tested all of the time). Instead, my preferred approach would be to have Cygwin users install a C++14 library (e.g. update their libstdc++ or install libc++, as documented in the README). Is this too difficult/impossible on Cygwin, or is this a reasonable requirement?  
3. Ensure the examples and unit tests build without support for exceptions, and at the same time test that the library itself does not use exceptions internally. This is a superb idea, and it is quite important because some potential clients of the library might not allow exceptions in their code. Also, as a metaprogramming library, it seems natural that exceptions should not be used in the implementation, and hence having a test configuration without exceptions seems desirable.  
  
Before modifying this PR further, let's resolve this and decide what we want/do not want.

---

## Comment 5

> Username: ldionne  
> Created_at: 2015-09-02 15:31:12 UTC  
> Url: https://github.com/boostorg/hana/pull/168#issuecomment-137134189  

Actually, regarding support for building without exceptions, I even wonder whether that shouldn't be the default? Clearly, we don't want to rely on exceptions _anywhere_ in Hana, so it seems like it would be more stringent to put `-fno-exceptions` whenever the flag is supported.

---

## Comment 6

> Username: m-j-w  
> Created_at: 2015-09-02 19:32:19 UTC  
> Url: https://github.com/boostorg/hana/pull/168#issuecomment-137221670  

Checks should be performed depending on the systems default, whether with of without exceptions. In the end, it is a library to be embedded in another application. Though, limiting hana itself to not rely on exceptions appears plausible.

---

## Comment 7

> Username: m-j-w  
> Created_at: 2015-09-08 18:52:44 UTC  
> Url: https://github.com/boostorg/hana/pull/168#issuecomment-138668631  

@ldionne, although already closed, here my quick confirmation: Hana compiles well and succeeds with the tests as long as mentioned type traits and to_string are not required.  
  
When including <boost/hana.hpp> and compiling some small dummy code, one receives the warning of C++14 not being provided. Luckily, this also is issued for gcc < 5.0 since then the __cplusplus equals 201300. Only clang pretends to fully comply with C++14 even when linking against libstdc++ < 5.0.   
So, looks as expected, looks good. Thanks !  
  
---  
  
Here the summary of the test build:  
  
```  
$ cmake -DCMAKE_CXX_COMPILER=clang++ ..  
$ make -i check after cmake  
```  
  
resulted in  
  
```  
99% tests passed, 13 tests failed out of 908  
Total Test time (real) =  19.36 sec  
The following tests FAILED:  
          9 - example.any_of (Not Run)  
         10 - example.ap (Not Run)  
         57 - example.eval_if (Not Run)  
        170 - example.overview (Not Run)  
        215 - example.sequence.monad.types (Not Run)  
        272 - example.tutorial.introspection.json (Not Run)  
        274 - example.tutorial.quickstart (Not Run)  
        277 - example.type.basic_type (Not Run)  
        280 - example.type.integral (Not Run)  
        295 - example.wandbox (Not Run)  
        377 - test.header.boost.hana.ext.std.type_traits.hpp (Not Run)  
        380 - test.header.boost.hana.ext.std.hpp (Not Run)  
        776 - test.ext.std.type_traits (Not Run)  
Errors while running CTest  
[100%] Built target check  
```

---

## Comment 8

> Username: ldionne  
> Created_at: 2015-09-08 20:07:07 UTC  
> Url: https://github.com/boostorg/hana/pull/168#issuecomment-138685010  

I checked all the failing tests/examples and I can understand why they would fail. However, I'm slightly surprised about `example.type.basic_type`. Is it because the `<cxxabi.h>` header is missing? I don't think this header is portable.  
  
Also note that `example.any_of` and `example.type.integral` included `<boost/hana/ext/std/type_traits.hpp>`, without requiring it strictly speaking. I changed that and they should pass now.

---

## Comment 9

> Username: m-j-w  
> Created_at: 2015-09-09 18:16:56 UTC  
> Url: https://github.com/boostorg/hana/pull/168#issuecomment-138996123  

The current status with the reason of non-compilation / missing symbol is as follows, your conclusions are correct, the last one is new however:  
  
```  
The following tests FAILED:  
         10 - example.ap (Not Run)    =>    ap.cpp:20:9: error: static_assert expression is not an integral constant expression  
         55 - example.eval_if (Not Run)    =>    std::is_trivially_copyable  
        168 - example.overview (Not Run)    =>    std::is_trivially_copyable  
        213 - example.sequence.monad.types (Not Run)    =>    std::is_trivially_copyable  
        270 - example.tutorial.introspection.json (Not Run)    =>    std::to_string  
        272 - example.tutorial.quickstart (Not Run)    =>    std::is_trivially_copyable  
        275 - example.type.basic_type (Not Run)    =>     error: no member named 'free' in namespace 'std';  abi::__cxa_demangle(mangled, 0, 0, &status), std::free  
        293 - example.wandbox (Not Run)    =>    std::to_string  
        374 - test.header.boost.hana.ext.std.type_traits.hpp (Not Run)   =>   std::is_trivially_copyable  
        377 - test.header.boost.hana.ext.std.hpp (Not Run)   =>   std::is_trivially_copyable  
        750 - test.ext.std.type_traits (Not Run)   =>   std::is_trivially_copyable  
        821 - test.pair.cnstr.copy (Not Run)    =>    std::is_trivially_copy_constructible  
Errors while running CTest  
[100%] Built target check  
```  
  
Anyway, I think it's fine, the important basic stuff is operational.

---

## Comment 10

> Username: ldionne  
> Created_at: 2015-09-09 19:18:00 UTC  
> Url: https://github.com/boostorg/hana/pull/168#issuecomment-139019327  

Ok, `example.type.basic_type` should be fixed for now, although it will have to be modified if we ever support MSVC (one can dream). Regarding `test.pair.cnstr.copy`, I just checked and it's also an expected failure (but the test was added just yesterday). Thanks a lot!

---
