# #208 - Unable to find file or target named '/boost//C:/Tools/vcpkg/installed... [Closed]

> Username: josch  
> Created at: 2019-08-06 22:05:36 UTC  
> Updated at: 2024-11-13 13:33:26 UTC  
> Closed at: 2019-08-07 05:11:57 UTC  
> Url: https://github.com/boostorg/fiber/issues/208  

Hi,  
  
when building boost-fiber via vcpkg on Windows with Visual Studio 2017 I'm getting the following error (from the log):  
  
```  
error: at C:/projects/vcpkg-boost-release-test/vcpkg/installed/x64-windows/tools/boost-build/src/kernel\modules.jam:107  
error: Unable to find file or target named  
error:     '/boost//C:/projects/vcpkg-boost-release-test/vcpkg/installed/x64-windows/debug/lib/boost_context-vc140-mt-gd.lib'  
error: referred to from project at  
error:     '..'  
dir installed\x64-windows\debug  
```  
  
I already reported the error to vcpkg [here](https://github.com/microsoft/vcpkg/issues/7559) but I suspect that this might be an upstream issue because the path seems to be wrongly concatenated? Look at line 3 in the output above and notice the `/boost//` prefix before the absolute path.  
  
The problem only occurs when doing a release build only (no debug build). Maybe you have an idea what could be going on?  
  
There are some full logs at [appveyor](https://ci.appveyor.com/project/josch/vcpkg-boost-release-test/builds/26516306/job/gi9bq43yylodnhj2).  
  
Thanks!

---

## Comment 1

> Username: olk  
> Created at: 2019-08-07 05:11:57 UTC  
> Url: https://github.com/boostorg/fiber/issues/208#issuecomment-518942749  

I'm sorry - I guess that the libs are not build because of some missing build flags (for instance C++11 is required!).

---

## Comment 2

> Username: josch  
> Created at: 2019-08-07 06:48:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/208#issuecomment-518964463  

I think it would be strange if the issue was missing build flags. All other boost components build just fine under vcpkg and visual studio 2017 and they also need c++11.  
  
Also, how should missing build flags lead to the wrong `/boost//` prefix in the path that can be seen above?  
  
You already closed this issue. Are you basically saying that you don't care?

---

## Comment 3

> Username: olk  
> Created at: 2019-08-07 06:55:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/208#issuecomment-518966301  

vcpkg does not belong to Boost - the official way to build Boost is b2/bjam.  
  
boost.fiber builds with b2/bjam (see https://www.boost.org/development/tests/master/developer/fiber.html) so I guess your issue is related to vcpkg/your config.  
  
I don't use vcpkg so can't tell much/resolve your issue.

---

## Comment 4

> Username: shezik  
> Created at: 2024-11-13 13:33:25 UTC  
> Url: https://github.com/boostorg/fiber/issues/208#issuecomment-2473637000  

I encountered a similar problem as well, but the underlying issue is likely to be different.  
Check out this comment: <https://github.com/microsoft/vcpkg/issues/38980#issuecomment-2137139379>
