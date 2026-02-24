# #22 - Build failure with MSVC 12.0 (VS2013) [Closed]

> Username: jeking3  
> Created at: 2019-06-04 12:34:41 UTC  
> Updated at: 2019-06-04 16:22:25 UTC  
> Closed at: 2019-06-04 16:22:25 UTC  
> Url: https://github.com/boostorg/contract/issues/22  

Seeing the following error with Visual Studio 2013:  
  
```  
compile-c-c++ bin.v2\libs\contract\example\features-throw_on_failure.test\msvc-12.0\release\threading-multi\features\throw_on_failure.obj  
throw_on_failure.cpp  
libs\contract\example\features\throw_on_failure.cpp(55) : error C3646: 'noexcept' : unknown override specifier  
        libs\contract\example\features\throw_on_failure.cpp(72) : see reference to class template instantiation 'cstring<MaxSize>' being compiled  
    call "bin.v2\standalone\msvc\msvc-12.0\msvc-setup.bat" x86 >nul  
 cl /Zm800 -nologo @"bin.v2\libs\contract\example\features-throw_on_failure.test\msvc-12.0\release\threading-multi\features\throw_on_failure.obj.rsp"   
...failed compile-c-c++ bin.v2\libs\contract\example\features-throw_on_failure.test\msvc-12.0\release\threading-multi\features\throw_on_failure.obj...  
```  
  
https://ci.appveyor.com/project/jeking3/contract/builds/25028138/job/89633x54cn1yu5ir?fullLog=true#L838  
  
There are no expected failures for MSVC 12.0 (VS2013) in the meta directory so I'm leaving this as an open issue.

---

## Comment 1

> Username: jeking3  
> Created at: 2019-06-04 13:14:43 UTC  
> Url: https://github.com/boostorg/contract/issues/22#issuecomment-498666366  

I found the explicit failures markup file in meta, and it looks like some of these issues are known.  I'm not certain the VS2013 issue is known however.  Could you verify?  
  
I'm going to mark VS 2010, 2012, and 2013 as "allowed failures" in the appveyor specification for now and attribute them to this GitHub issue.  
  
Preview of the builds CI will run:  
  
https://ci.appveyor.com/project/jeking3/contract/builds/25028138  
https://travis-ci.org/jeking3/contract/builds/541174205?utm_source=github_status&utm_medium=notification

---

## Comment 2

> Username: jeking3  
> Created at: 2019-06-04 13:18:31 UTC  
> Url: https://github.com/boostorg/contract/issues/22#issuecomment-498667787  

Actually given the extensiveness of the MSVC 10.0 and 11.0 (VS2010, VS2012) issues I am going to remove them from the build entirely.  I see no expected failures for MSVC 12.0 (VS2013) and there is an issue so I am going to leave it as an allowed failure and reference this issue.

---

## Comment 3

> Username: lcaminiti  
> Created at: 2019-06-04 15:59:35 UTC  
> Url: https://github.com/boostorg/contract/issues/22#issuecomment-498734564  

Both MSVC 10 and 11 work (a part from a couple of expected failures), it would be good to leave those in. Look at the tests here (scroll all the way to the right and look for msvc-10 and msvc-11):  
https://www.boost.org/development/tests/develop/developer/contract.html  
  
If you are getting errors for the example/* just skip those and run only the test/* (some examples are intentionally artificial because I used them in the docs to explain how to use the library, they don't really mean much).

---

## Comment 4

> Username: lcaminiti  
> Created at: 2019-06-04 16:05:36 UTC  
> Url: https://github.com/boostorg/contract/issues/22#issuecomment-498736923  

The errors C3646 are because these compilers do not make destructors noexcept by default (since C++11). These errors are only in example/*, but I won't change the example code to use BOOST_NOEXCEPT because that will make these documentation examples harder to read. The tests test/* instead use this macro and more so they run on all platform. Do not add the examples example/* to CI, just add the tests test/* instead.  
  
I will look double check all links in other comments in this issue because closing it.

---

## Comment 5

> Username: jeking3  
> Created at: 2019-06-04 16:08:58 UTC  
> Url: https://github.com/boostorg/contract/issues/22#issuecomment-498738259  

It's pretty standard practice to ensure examples work, (all 15 CMT repositories and 3 I maintain directly do this) but I will remove them from the CI builds here.

---

## Comment 6

> Username: lcaminiti  
> Created at: 2019-06-04 16:22:25 UTC  
> Url: https://github.com/boostorg/contract/issues/22#issuecomment-498743233  

The examples do work. However, they are not designed to be cross compiler/platform because that would make them less readable, instead their main purpose is to show case library features with simple code (without Boost.Config macros and other tricks needed to support compiler from C++98 to C++20 on Linux, Windows, Mac, and all these other combinations).  
Tests are designed to fully test all library features and also the be cross compiler/platform.
