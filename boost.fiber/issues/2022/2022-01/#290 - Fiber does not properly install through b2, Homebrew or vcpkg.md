# #290 - Fiber does not properly install through b2, Homebrew or vcpkg [Closed]

> Username: denvercoder21  
> Created at: 2022-01-28 18:14:42 UTC  
> Updated at: 2022-01-29 13:58:13 UTC  
> Closed at: 2022-01-29 09:14:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/290  

Hello, I recently switched from Ubuntu to macOS and can't seem to correctly install Fiber.  
  
Platform: Apple M1, macOS 12.2, [gcc-11](https://formulae.brew.sh/formula/gcc#default)  
  
First, I tried downloading boost myself and installing via b2:  
```  
./bootstrap.sh  
sudo ./b2 install  
```  
Which didn't yield any errors. However, trying to run the CMake configuration for an example project that only uses Boost Fiber failed with message "Boost Fiber not found".  
  
I googled for the problem and found this [StackOverflow post](https://stackoverflow.com/questions/62440949/do-anyone-know-why-i-build-boost-on-macos-but-theres-no-boostfiber-lib-create), so I tried:  
```  
./bootstrap.sh --with-libraries=all  
sudo ./b2 cxxflags=-std=c++17 install -a > out.txt 2>&1  
```  
CMake configuration now worked, so the necessary `.cmake` file got installed. But when building I got the error that `libboost_fiber.dylib` could not be found. Upon investigation, I found that the `.dylib` file indeed wasn't installed to `/usr/local/...`, same as for the `.a` file.    
I did find these files in the following folder of my downloaded boost archive:    
```  
/bin.v2/libs/fiber/build/clang-darwin-13/release/threading-multi/visibility-hidden  
```  
So I copied both files manually into the correct folder in `/usr/local/...`. Building my example project now worked, but when running it I was getting a segfault.  
  
After this I removed all boost-related files from `/usr/local/...` and installed boost using [Homebrew](https://formulae.brew.sh/formula/boost#default):  
```  
brew install homebrew  
```  
~~Again, the CMake files were all there, but building failed because of the missing fiber `.dylib` file. Also the `.a` file was not installed, too.~~  
The files get installed correctly, CMake configuration and building works. When running the example project, I'm seeing a segmentation fault.  
  
Next up I tried installing using vcpkg which failed with the following message:  
```  
cody@obelix ~ % cd dev/libs   
cody@obelix libs % git clone https://github.com/microsoft/vcpkg.git  
Cloning into 'vcpkg'...  
remote: Enumerating objects: 141129, done.  
remote: Counting objects: 100% (94/94), done.  
remote: Compressing objects: 100% (21/21), done.  
remote: Total 141129 (delta 81), reused 73 (delta 73), pack-reused 141035  
Receiving objects: 100% (141129/141129), 50.45 MiB | 15.22 MiB/s, done.  
Resolving deltas: 100% (88014/88014), done.  
cody@obelix libs % cd vcpkg   
cody@obelix vcpkg % ./bootstrap-vcpkg.sh   
Downloading vcpkg-macos...  
Telemetry  
---------  
vcpkg collects usage data in order to help us improve your experience.  
The data collected by Microsoft is anonymous.  
You can opt-out of telemetry by re-running the bootstrap-vcpkg script with -disableMetrics,  
passing --disable-metrics to vcpkg on the command line,  
or by setting the VCPKG_DISABLE_METRICS environment variable.  
  
Read more about vcpkg telemetry at docs/about/privacy.md  
cody@obelix vcpkg % ./vcpkg install boost-fiber  
Computing installation plan...  
Error: boost-fiber[core] is only supported on '!osx & !uwp & !arm & !emscripten'  
cody@obelix vcpkg %   
```  
  
This could be just an installation issue, but what confuses me is that even after manually copying the files into the correct folder, I'm getting a segfault.

---

## Comment 1

> Username: denvercoder21  
> Created at: 2022-01-29 09:14:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/290#issuecomment-1024873152  

I tried using Apple Clang and my example project in combination with a Homebrew installation of Boost works fine.  
There are still open issues:  
- Why do my applications segfault when compiled with gcc-11?  
- Using gcc-11, linking of a different project using Boost Fiber yields an error:  
```  
FAILED: bin/examples/MsgQueue_Ex1   
: && /opt/homebrew/bin/g++-11 -g -arch arm64 -isysroot /Library/Developer/CommandLineTools/SDKs/MacOSX12.1.sdk -Wl,-search_paths_first -Wl,-headerpad_max_install_names  examples/queues/CMakeFiles/MsgQueue_Ex1.dir/MsgQueue_Ex1.cpp.o -o bin/examples/MsgQueue_Ex1  lib/libbln.a  /opt/homebrew/lib/libboost_fiber-mt.dylib  /opt/homebrew/lib/libboost_context-mt.dylib  /opt/homebrew/lib/libboost_filesystem-mt.dylib && :  
Undefined symbols for architecture arm64:  
  "__ZN5boost6fibers10wait_queue22suspend_and_wait_untilERSt11unique_lockINS0_6detail13spinlock_ttasEEPNS0_7contextERKNSt6chrono10time_pointINS9_3_V212steady_clockENS9_8durationIxSt5ratioILl1ELl1000000000EEEEEE", referenced from:  
      __ZN5boost6fibers16buffered_channelIiE14pop_wait_untilINSt6chrono3_V212steady_clockENS4_8durationIxSt5ratioILl1ELl1000000000EEEEEENS0_17channel_op_statusERiRKNS4_10time_pointIT_T0_EE in MsgQueue_Ex1.cpp.o  
ld: symbol(s) not found for architecture arm64  
```  
- Why does `sudo ./b2 install` not install the `.dylib` and `.a` file?  
- Why does vcpkg fail?  
  
They all seem like separate problems that very, very likely are unrelated to Boost Fiber itself.

---

## Comment 2

> Username: olk  
> Created at: 2022-01-29 10:09:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/290#issuecomment-1024881707  

I don't own a Mac-system so I can't reproduce your issue...  
The Cmake stuff was done by @pdimov - I hope he can fix the problem of failed lib installation.  
  
I do not cross compile ... take a look at https://www.boost.org/doc/libs/1_78_0/libs/context/doc/html/context/architectures/crosscompiling.html  
  
You have to specify for a Mac-OS b2 arch=ar64 adress-model=64 binary-format=aapcs abi=mach-o

---

## Comment 3

> Username: pdimov  
> Created at: 2022-01-29 13:58:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/290#issuecomment-1024916816  

This (the .dylib/.a not getting installed) may be caused by https://github.com/boostorg/boost_install/issues/55 and https://github.com/bfgroup/b2/issues/104. (Basically, due to a bug in b2, the fact that `boost_fiber_numa` wasn't built prevented `boost_fiber` from being installed.)  
  
No idea about the segfault.
