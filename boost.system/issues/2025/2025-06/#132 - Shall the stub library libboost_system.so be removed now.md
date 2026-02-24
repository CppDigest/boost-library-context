# #132 - Shall the stub library libboost_system.so be removed now ? [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2025-06-26 09:17:40 UTC  
> Updated at: 2025-12-19 00:26:50 UTC  
> Closed at: 2025-12-19 00:26:50 UTC  
> Url: https://github.com/boostorg/system/issues/132  

Hi,  
  
Following the issue https://github.com/boostorg/filesystem/issues/336 on Boost.Filesystem side, I was wondering: does it still make sense in 2025 that Boost.System ships a stub linkable library while it has been header-only for years now ? Shouldn't we delete this compatibility layer now ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: pdimov  
> Created at: 2025-06-26 12:20:53 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3008296662  

Good point. It didn't do any harm before, but now that we have the modular build.jam, we should probably make it header-only in b2 as well.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-06-26 14:23:09 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3008680961  

Done on develop.

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created at: 2025-06-26 17:03:15 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3009149532  

Thanks, it seems to work when applied to the Boost 1.88 release.  
  
I have also opened a similar request for Boost.Regex in https://github.com/boostorg/regex/issues/253

---

## Comment 4

> Username: pdimov  
> Created at: 2025-06-26 17:20:57 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3009206000  

DateTime is header-only as well nowadays. But we haven't applied the [modular PR](https://github.com/boostorg/date_time/pull/239) there yet.

---

## Comment 5

> Username: mvieth  
> Created at: 2025-08-01 08:11:09 UTC  
> Updated at: 2025-08-01 12:52:47 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3143664322  

Hello, is it expected and intended behaviour that the file boost_system-config.cmake is now not installed any more? Up until Boost 1.88.0 it was available. Is there a recommended way to find out whether boost_system is installed, i.e. whether the boost system headers are available (in CMakeLists.txt)?

---

## Comment 6

> Username: pdimov  
> Created at: 2025-08-02 09:06:09 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3146378680  

Easiest would be to just drop `system` from the `COMPONENTS` as it's not needed since 1.69. (I assume your CML has something like `find_package(Boost COMPONENTS system foo bar)`.)  
  
If you care about Boost releases earlier than 1.69, you can use `OPTIONAL COMPONENTS system`, but I hope you don't. :-)

---

## Comment 7

> Username: crueter  
> Created at: 2025-10-21 02:56:00 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3424476260  

> Easiest would be to just drop `system` from the `COMPONENTS` as it's not needed since 1.69. (I assume your CML has something like `find_package(Boost COMPONENTS system foo bar)`.)  
>   
> If you care about Boost releases earlier than 1.69, you can use `OPTIONAL COMPONENTS system`, but I hope you don't. :-)  
  
For future reference, please mention breaking changes like this in the changelog itself. I should not have to dig through GitHub issues (well, I shouldn't have to use GitHub, period) to find out what caused this.

---

## Comment 8

> Username: pdimov  
> Created at: 2025-10-21 14:59:21 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3427134627  

A good idea in retrospect, but it didn't occur to me at the time, sorry.

---

## Comment 9

> Username: Romain-Geissler-1A  
> Created at: 2025-10-21 15:01:23 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3427144515  

Since the initial issue is implemented, I will close this ticket.

---

## Comment 10

> Username: jwakely  
> Created at: 2025-12-09 16:25:17 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3633141663  

> A good idea in retrospect, but it didn't occur to me at the time, sorry.  
  
Could the release notes at https://www.boost.org/releases/1.89.0/ be updated to mention it?

---

## Comment 11

> Username: pdimov  
> Created at: 2025-12-10 10:46:19 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3636468054  

We'll try. It's nontrivial because when 1.89 was released we were still using the old site and the old release notes format.

---

## Comment 12

> Username: jwakely  
> Created at: 2025-12-15 19:06:44 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3657160965  

Removing this stub library is extremely disruptive for Fedora, as most of the packages that depend on Boost fail to build with errors like:  
  
```  
CMake Error at /usr/lib64/cmake/Boost-1.90.0/BoostConfig.cmake:141 (find_package):  
  Could not find a package configuration file provided by "boost_system"  
  (requested version 1.90.0) with any of the following names:  
  
    boost_systemConfig.cmake  
    boost_system-config.cmake  
  
  Add the installation prefix of "boost_system" to CMAKE_PREFIX_PATH or set  
  "boost_system_DIR" to a directory containing one of the above files.  If  
  "boost_system" provides a separate development package or SDK, be sure it  
  has been installed.  
Call Stack (most recent call first):  
  /usr/lib64/cmake/Boost-1.90.0/BoostConfig.cmake:262 (boost_find_component)  
  /usr/share/cmake/Modules/FindBoost.cmake:610 (find_package)  
  CMakeLists.txt:500 (find_package)  
```  
  
I think I'm going to add a stub library back just so everything can continue "requiring" it.

---

## Comment 13

> Username: pdimov  
> Created at: 2025-12-15 19:43:58 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3657310290  

I wonder whether I can compromise by still "installing" `boost_system`, even though it doesn't exist, so that there's still `boost_system-config.cmake` to be found.

---

## Comment 14

> Username: pdimov  
> Created at: 2025-12-15 19:55:33 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3657346748  

Please try https://github.com/boostorg/boost_install/commit/d9554db26c3dbb00a6a293ee4fd4966e4e278da8 and https://github.com/boostorg/system/commit/9529e070ea9e9afd1da17edd48993d560fdc0d7a and see if that helps.  
  
A physical library (libboost_system.a/.so) is still not going to be built, but there should be a CMake config, and it should work at least some of the time, hopefully even most of the time.

---

## Comment 15

> Username: jwakely  
> Created at: 2025-12-15 20:05:15 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3657378658  

Thanks! I'll give that a try. It would have taken me much longer to figure out how to get cmake to do that.  
  
There are a handful of Fedora packages which seem to just use `-lboost_system` directly, without checking for it in cmake. There are a small enough number that we can fix them individually, but a no-op cmake config should help for the vast majority.

---

## Comment 16

> Username: jwakely  
> Created at: 2025-12-16 13:20:03 UTC  
> Url: https://github.com/boostorg/system/issues/132#issuecomment-3660491833  

This works nicely - thanks very much for helping so quickly.
