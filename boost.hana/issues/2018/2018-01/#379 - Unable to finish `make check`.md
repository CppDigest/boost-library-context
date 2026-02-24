# #379 - Unable to finish `make check` [Closed]

> Username: ricejasonf  
> Created at: 2018-01-03 00:13:54 UTC  
> Updated at: 2018-01-07 17:26:53 UTC  
> Closed at: 2018-01-07 17:26:53 UTC  
> Url: https://github.com/boostorg/hana/issues/379  

Everything compiles until the very last target.  
  
The `test.deploy.fakeroot` target appears to require `libstd++` which is not installed in the environment I am building with. I am using a toolchain file that specifies libc++.  
  
This is the output:  
  
```  
[100%] Performing configure step for 'test.deploy.fakeroot'  
-- The CXX compiler identification is Clang 4.0.0  
-- Check for working CXX compiler: /opt/sysroot/bin/clang++  
-- Check for working CXX compiler: /opt/sysroot/bin/clang++ -- broken  
CMake Error at /usr/share/cmake-3.9/Modules/CMakeTestCXXCompiler.cmake:44 (message):  
  The C++ compiler "/opt/sysroot/bin/clang++" is not able to compile a simple  
  test program.  
  
  It fails with the following output:  
  
   Change Dir: /opt/build/test/test.deploy.fakeroot-prefix/src/test.deploy.fakeroot-build/CMakeFiles/CMakeTmp  
  
    
  
  Run Build Command:"/usr/bin/make" "cmTC_1236a/fast"  
  
  make[4]: Entering directory  
  '/opt/build/test/test.deploy.fakeroot-prefix/src/test.deploy.fakeroot-build/CMakeFiles/CMakeTmp'  
  
  
  /usr/bin/make -f CMakeFiles/cmTC_1236a.dir/build.make  
  CMakeFiles/cmTC_1236a.dir/build  
  
  make[5]: Entering directory  
  '/opt/build/test/test.deploy.fakeroot-prefix/src/test.deploy.fakeroot-build/CMakeFiles/CMakeTmp'  
  
  
  Building CXX object CMakeFiles/cmTC_1236a.dir/testCXXCompiler.cxx.o  
  
  /opt/sysroot/bin/clang++ -o CMakeFiles/cmTC_1236a.dir/testCXXCompiler.cxx.o  
  -c  
  /opt/build/test/test.deploy.fakeroot-prefix/src/test.deploy.fakeroot-build/CMakeFiles/CMakeTmp/testCXXCompiler.cxx  
  
  
  Linking CXX executable cmTC_1236a  
  
  /usr/bin/cmake -E cmake_link_script CMakeFiles/cmTC_1236a.dir/link.txt  
  --verbose=1  
  
  /opt/sysroot/bin/clang++ CMakeFiles/cmTC_1236a.dir/testCXXCompiler.cxx.o -o  
  cmTC_1236a  
  
  /usr/bin/ld: cannot find -lstdc++  
  
  clang-4.0: error: linker command failed with exit code 1 (use -v to see  
  invocation)  
  
  CMakeFiles/cmTC_1236a.dir/build.make:97: recipe for target 'cmTC_1236a'  
  failed  
  
  make[5]: *** [cmTC_1236a] Error 1  
  
  make[5]: Leaving directory  
  '/opt/build/test/test.deploy.fakeroot-prefix/src/test.deploy.fakeroot-build/CMakeFiles/CMakeTmp'  
  
  
  Makefile:126: recipe for target 'cmTC_1236a/fast' failed  
  
  make[4]: *** [cmTC_1236a/fast] Error 2  
  
  make[4]: Leaving directory  
  '/opt/build/test/test.deploy.fakeroot-prefix/src/test.deploy.fakeroot-build/CMakeFiles/CMakeTmp'  
  
  
    
  
    
  
  CMake will not be able to correctly generate this project.  
Call Stack (most recent call first):  
  CMakeLists.txt:40 (project)  
  
  
-- Configuring incomplete, errors occurred!  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2018-01-06 18:50:00 UTC  
> Url: https://github.com/boostorg/hana/issues/379#issuecomment-355767168  

I think this means we should be passing `-DCMAKE_TOOLCHAIN_FILE` to the deploy target.

---

## Comment 2

> Username: ldionne  
> Created at: 2018-01-06 18:53:54 UTC  
> Url: https://github.com/boostorg/hana/issues/379#issuecomment-355767413  

Can you try out again with this patch and let me know how that works? https://github.com/boostorg/hana/tree/5891bccb49ea6a629dfb33c1654c109066294b3e

---

## Comment 3

> Username: ricejasonf  
> Created at: 2018-01-06 20:31:04 UTC  
> Url: https://github.com/boostorg/hana/issues/379#issuecomment-355774077  

That works for me, but I had to add it to `test.deploy.fakeroot` as well.  
  
Feel free to fix this up or I can make PR or whatever: https://github.com/ricejasonf/hana/commits/pr/toolchain  
  
Thanks!

---

## Comment 4

> Username: ldionne  
> Created at: 2018-01-07 17:26:53 UTC  
> Url: https://github.com/boostorg/hana/issues/379#issuecomment-355838127  

Closed by #382
