# #39 - Boost::python: missing `init_module` [Closed]

> Username: Axel-Naumann  
> Created at: 2017-12-12 09:31:52 UTC  
> Updated at: 2018-03-10 19:26:48 UTC  
> Closed at: 2018-03-10 19:26:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/39  

With  
```  
-- Boost version: 1.65.1  
-- Found the following Boost libraries:  
--   iostreams  
--   serialization  
--   python  
--   numpy  
-- Build type: RELEASE [optimizations on]  
```  
I get  
```  
Undefined symbols for architecture x86_64:  
  "boost::python::detail::init_module(PyModuleDef&, void (*)())", referenced from:  
      _PyInit_cpp_filler in guide_listing_16.cpp.o  
ld: symbol(s) not found for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
make[2]: *** [cpp_filler.so] Error 1  
```  
That's on MacOS 10.13.1 with Xcode 9.2

---

## Comment 1

> Username: HDembinski  
> Created at: 2017-12-12 10:42:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/39#issuecomment-351013639  

Hm, I am also developing on Mac, using the exact same setup. Do you have two installations of Python? It is important that both boost.python and this library are linked to the same version of the Python libraries.

---

## Comment 2

> Username: HDembinski  
> Created at: 2017-12-12 10:42:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/39#issuecomment-351013855  

See https://github.com/davisking/dlib/issues/80 and solution

---

## Comment 3

> Username: HDembinski  
> Created at: 2017-12-12 10:44:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/39#issuecomment-351014336  

If you have both Python 2 and 3 installed, cmake will probably detect Python 3. To force Python 2, you can pass the PYTHON_VERSION flag:  
`cmake -DPYTHON_VERSION=2 [...]`  
I am sorry for this inconvenience :(

---

## Comment 4

> Username: HDembinski  
> Created at: 2017-12-13 10:49:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/39#issuecomment-351355085  

I added a note in the README.md about that issue. Does it work now?

---

## Comment 5

> Username: HDembinski  
> Created at: 2017-12-13 12:33:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/39#issuecomment-351378111  

I further improved the build script to support python3 on Mac. It seems that Boost.Python uses the name python3 for the python component if it was compiled against Python3. So I can detect that. I cannot detect whether Boost.Numpy is also compiled to the correct Python libraries.

---

## Comment 6

> Username: Axel-Naumann  
> Created at: 2017-12-13 19:11:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/39#issuecomment-351491709  

This is probably just the usual CMake weirdness:  
```  
○ → cmake -DBOOST_ROOT=/Users/axel/build/boost/inst/ ../src/build/  
-- The CXX compiler identification is AppleClang 9.0.0.9000039  
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++  
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found PythonLibs: /Library/Frameworks/Python.framework/Versions/3.5/lib/libpython3.5m.dylib (found version "3.5.1")  
-- Found PythonInterp: /usr/bin/python (found version "2.7.10")  
-- Python lib: /Library/Frameworks/Python.framework/Versions/3.5/lib/libpython3.5m.dylib  
-- Python include: /Library/Frameworks/Python.framework/Versions/3.5/include/python3.5m  
```  
  
I *think* I got the same error with Boost and your package using the same Python3 installation.   
  
`-DBUILD_PYTHON=Off` does it for now...

---

## Comment 7

> Username: HDembinski  
> Created at: 2017-12-14 12:34:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/39#issuecomment-351698835  

Which cmake version are you using? In my case it is 3.9.6. In this version, find_package(PythonInterp) looks for the version string found by find_package(PythonLibs) automatically. I just committed a change to the CMakeLists.txt, to make sure this is done. I hope this helps.  
Could you please try to update your working copy to current master, wipe the build directory completely, then do `cmake -DBOOST_ROOT=/Users/axel/build/boost/inst/ ../src/build/ -DPYTHON_VERSION=2` and let me know whether that works?

---

## Comment 8

> Username: HDembinski  
> Created at: 2018-03-06 21:47:23 UTC  
> Url: https://github.com/boostorg/histogram/issues/39#issuecomment-370940335  

@Axel-Naumann could you try again with the latest master and the new instructions in the README?

---

## Comment 9

> Username: HDembinski  
> Created at: 2018-03-10 19:26:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/39#issuecomment-372058131  

I assume the issue is fixed, please re-open if not :)
