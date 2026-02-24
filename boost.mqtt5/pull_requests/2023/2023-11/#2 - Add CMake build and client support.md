# #2 Add CMake build and client support [Merged]

> Username: friendlyanon  
> Created at: 2023-11-28 23:56:28 UTC  
> Updated at: 2024-01-04 12:20:18 UTC  
> Merged at: 2024-01-04 12:20:18 UTC  
> Closed at: 2024-01-04 12:20:18 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2  

What I need feedback on:  
  
* What should be the package name of this project for CMake clients? Right now it's `async-mqtt5`, i.e. the package can be found with `find_package(async-mqtt5 REQUIRED)`  
* What should the exported target name be? Right now it's `Async::MQTT5`.  
  
I would've added GitHub Actions CI as well, but it seems the project has issues running its tests on Windows.    
Note that the project requires some additional defines on Windows to be set by the person configuring from the command line via `-DCMAKE_CXX_FLAGS=...` and that is good and must not be hardcoded into project code, because long term that is a fool's errand.

---

## Comment 1

> Username: ksimicevic  
> Created_at: 2023-11-29 14:53:59 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#issuecomment-1832049521  

Hello!  
  
Thank you very much for your contribution! I'll try it out tomorrow.   
  
As for your questions, both names are fine right now.

---

## Comment 2

> Username: friendlyanon  
> Created_at: 2023-11-29 18:54:15 UTC  
> Updated_at: 2023-11-29 19:01:54 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#issuecomment-1832513361  

The command line I built the project with as a developer on Windows in a `-arch=amd64 -host_arch=amd64` `vcvars` enabled command prompt is:  
  
```  
cmake -B build -G Ninja -D async-mqtt5_DEVELOPER_MODE=1 -D CMAKE_BUILD_TYPE=Release -D "CMAKE_CXX_FLAGS=/sdl /guard:cf /utf-8 /diagnostics:caret /w14165 /w44242 /w44254 /w44263 /w34265 /w34287 /w44296 /w44365 /w44388 /w44464 /w14545 /w14546 /w14547 /w14549 /w14555 /w34619 /w34640 /w24826 /w14905 /w14906 /w14928 /w45038 /W4 /permissive- /volatile:iso /Zc:inline /Zc:preprocessor /Zc:enumTypes /Zc:lambda /Zc:__cplusplus /Zc:externConstexpr /Zc:throwingNew /EHsc /D_WIN32_WINNT=0x0A00 /DNTDDI_VERSION=0x0A000007 /DWIN32_LEAN_AND_MEAN=1 /DNOMINMAX=1 /bigobj" -D "CMAKE_EXE_LINKER_FLAGS=/machine:x64 /guard:cf" -D CMAKE_CXX_EXTENSIONS=0 -D CMAKE_CXX_STANDARD=20 -D CMAKE_CXX_STANDARD_REQUIRED=1 --toolchain "%VCPKG_ROOT%/scripts/buildsystems/vcpkg.cmake" -D VCPKG_MANIFEST_FEATURES=test  
cmake --build build >build.log 2>&1  
```  
  
Redirecting the build output into a file is recommended, because the output from the warnings is plentiful. Produces a 10 MB output on my machine.  
  
Don't mind the long configure command, it can be moved into a [preset](https://github.com/friendlyanon/cmake-init-header-only/blob/master/CMakePresets.json) at any time.  
  
Producing a CMake package is also simple:  
  
```  
cmake --install build --prefix prefix  
```  
  
So is running tests:  
  
```  
ctest --test-dir build --output-on-failure --no-tests=error  
```  
  
Although the above will hang as noted in the test comments.

---

## Comment 3

> Username: friendlyanon  
> Created_at: 2023-11-29 19:47:04 UTC  
> Updated_at: 2023-11-29 19:52:04 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#issuecomment-1832598762  

Just a curiosity, I also tried to build with a couple other compilers on Windows as well:  
  
* LLVM Clang 17.0.5: fails around the coroutine code  
* ClangCL: fails around the coroutine code  
* MinGW x86_64-13.2.0-release-win32-seh-ucrt-rt_v11-rev0: ICE around the coroutine code

---

## Comment 4

> Username: ksimicevic  
> Created_at: 2023-11-30 10:43:26 UTC  
> Updated_at: 2023-11-30 10:46:22 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#issuecomment-1833510084  

Hello!  
  
Once again, thank you very much for your contribution!  
Could we kindly ask you to make the following changes to reflect our requirements:  
  
- Dependency on the OpenSSL library is optional. It is only required when the `mqtt_client` needs an SSL connection to the broker. In our case, it is only required for files: `example/openssl-tls.cpp` and `example/websocket-tls.cpp`. We want to be able to define a flag when we need the dependency on the OpenSSL and omit it when we do not.  
- We only require the headers from Boost. So I'd suggest something like this:  
```  
find_package(Boost 1.82 REQUIRED COMPONENTS headers)  
target_link_libraries(async_mqtt5 INTERFACE OpenSSL::SSL Boost::headers)  
```  
- In the `example` folder, several source files exist solely for documentation purposes. Those include `network_connection.cpp`, `callbacks.cpp`, `futures.cpp`, and  `cpp20_coroutines.cpp`. The `network_connection.cpp` file is not meant to be compiled. The `callbacks.cpp` file is not meant to be executed (the client will close immediately). The `callbacks.cpp` file references how to use the `mqtt_client` with callbacks in the documentation. The mentioned files will be moved to the appropriate folder soon to avoid confusion.  
  
I will take a look at the build errors you have encountered with other compilers on Windows.

---

## Comment 5

> Username: friendlyanon  
> Created_at: 2023-11-30 13:45:17 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#issuecomment-1833812739  

> Dependency on the OpenSSL library is optional.  
  
Since this is a header-only library (for now), it should be documented that the user should find and link OpenSSL if they need the features. Putting this behind a flag for a header-only library is absolutely pointless and just makes project code more complex for no benefit.  
  
> In the `example` folder, several source files exist solely for documentation purposes.  
  
That explains why they are giving problems. I think they ought to be buildable, otherwise you will not know if they are actually correct.

---

## Comment 6

> Username: ksimicevic  
> Created_at: 2023-11-30 14:15:18 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#issuecomment-1833861979  

> Since this is a header-only library (for now), it should be documented that the user should find and link OpenSSL if they need the features. Putting this behind a flag for a header-only library is absolutely pointless and just makes project code more complex for no benefit.  
  
The fact that OpenSSL is optional is documented in multiple places. However, I suppose it wouldn't hurt to note that in the CMakeLists.txt as well. Right now, a comment would be good enough.   
  
> That explains why they are giving problems. I think they ought to be buildable, otherwise you will not know if they are actually correct.  
  
Of course! All the files are buildable except `network_connection.cpp`, which will be buildable if you comment out external customization points for TLS (for example, lines 139-156) as they are repeated for TLS and WebSocket over TLS examples. Creating multiple files for this case alone seemed like an overkill.

---

## Review 7 [Commented]

> Username: friendlyanon  
> Created_at: 2023-11-30 17:09:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mqtt5/pull/2#pullrequestreview-1757973695  

📁 example/CMakeLists.txt

```diff
  19 |+ endforeach()
  20 |+ 
  21 |+ #[[
```

> Username: friendlyanon  
> Created_at: 2023-11-30 17:09:41 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#discussion_r1410982257  

I left this part in case you want to make this code compile. I can delete it if you want.


---

## Comment 8

> Username: ksimicevic  
> Created_at: 2023-12-04 10:46:18 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#issuecomment-1838325425  

> Just a curiosity, I also tried to build with a couple other compilers on Windows as well:  
>   
>     * LLVM Clang 17.0.5: fails around the coroutine code  
>   
>     * ClangCL: fails around the coroutine code  
>   
>     * MinGW x86_64-13.2.0-release-win32-seh-ucrt-rt_v11-rev0: ICE around the coroutine code  
  
Boost is only tested on Microsoft Visual C++ compilers on Windows.   
  
Source: https://www.boost.org/users/history/version_1_82_0.html (Section _Compilers Tested_ at the bottom of the page)

---

## Comment 9

> Username: biljazovic  
> Created_at: 2023-12-05 11:22:40 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#issuecomment-1840554534  

Hello,  
  
I tried to build this with vcpkg toolchain and tests fail to build because `Boost::unit_test_framework` isn't compiled with `BOOST_TEST_NO_MAIN` defined. I'm not familiar with CMake or vcpkg, how does one modify build of vcpkg-managed dependencies?

---

## Comment 10

> Username: friendlyanon  
> Created_at: 2023-12-07 01:47:05 UTC  
> Updated_at: 2023-12-07 01:48:10 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/2#issuecomment-1844024444  

vcpkg defaults to building dynamic libraries on Windows and static libraries on Linux and it seems Boost.Test is one of the libraries where dynamic linking makes changes to the API.  
  
I found a solution that works for both shared and static builds on MSVC and GCC 13.2 on Linux (although this one can't build the coroutine tests without ICE).

---
