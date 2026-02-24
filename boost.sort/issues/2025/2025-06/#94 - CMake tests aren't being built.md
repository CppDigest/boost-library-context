# #94 - CMake tests aren't being built [Closed]

> Username: pdimov  
> Created at: 2025-06-08 19:59:38 UTC  
> Updated at: 2025-06-28 16:59:47 UTC  
> Closed at: 2025-06-28 16:59:47 UTC  
> Url: https://github.com/boostorg/sort/issues/94  

Please make sure that the CMake tests are built when the target `tests` is built, per the procedure here https://github.com/boostorg/cmake?tab=readme-ov-file#testing-boost-with-cmake. Not doing so leads to CI failures, e.g. https://github.com/boostorg/boost/actions/runs/15521428592/job/43695126948

---

## Comment 1

> Username: spreadsort  
> Created at: 2025-06-08 23:33:27 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-2954330610  

Are you saying we need to modify test/CMakeLists.txt or test/Jamfile.v2?  I'm not sure what you're asking for.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-06-11 18:47:25 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-2963832339  

test/CMakeLists.txt. See how it's been done in https://github.com/boostorg/pfr/pull/207 and do the same.

---

## Comment 3

> Username: spreadsort  
> Created at: 2025-06-18 00:16:49 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-2982188302  

Is this what you were looking for: https://github.com/boostorg/sort/commit/04dcfafd05cc52c0b76fefeaf2e96ffcc2e17f5d?

---

## Comment 4

> Username: pdimov  
> Created at: 2025-06-18 06:00:49 UTC  
> Updated at: 2025-06-18 06:01:12 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-2982797002  

Not really. First, you shouldn't be doing `find_package(Boost)` - this finds some random Boost installation and we want the current superproject of which we're a part. Second, the target `tests` may already have been created (and usually has been), so defining it again is going to be an error.  
  
Again, the goal is that the procedure described [here](https://github.com/boostorg/cmake?tab=readme-ov-file#testing-boost-with-cmake) works. To repeat it here:  
  
From the Boost superproject root,  
  
```  
mkdir __build__  
cd __build__  
cmake -DBUILD_TESTING=ON -DBOOST_INCLUDE_LIBRARIES=sort ..  
cmake --build . --target tests  
ctest --output-on-failure --no-tests=error  
```

---

## Comment 5

> Username: spreadsort  
> Created at: 2025-06-19 09:30:53 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-2987407992  

Reverted that change.  
Tests does not exist:  
rm -Rf __build__                                            
sjr@Mac sort % mkdir __build__                                             
cd __build__  
sjr@Mac __build__ % cmake -DBUILD_TESTING=ON -DBOOST_INCLUDE_LIBRARIES=sort ..  
-- The CXX compiler identification is AppleClang 17.0.0.17000013  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Configuring done (0.2s)  
-- Generating done (0.0s)  
CMake Warning:  
  Manually-specified variables were not used by the project:  
  
    BOOST_INCLUDE_LIBRARIES  
  
  
-- Build files have been written to: /Users/sjr/boost/boost_1_88_0/libs/sort/__build__  
sjr@Mac __build__ % cmake --build . --target tests                              
make: *** No rule to make target `tests'.  Stop.  
  
If I add it back:  
mkdir __build__                                             
cd __build__  
sjr@Mac __build__ % cmake -DBUILD_TESTING=ON -DBOOST_INCLUDE_LIBRARIES=sort ..  
-- The CXX compiler identification is AppleClang 17.0.0.17000013  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Configuring done (0.2s)  
-- Generating done (0.0s)  
CMake Warning:  
  Manually-specified variables were not used by the project:  
  
    BOOST_INCLUDE_LIBRARIES  
  
  
-- Build files have been written to: /Users/sjr/boost/boost_1_88_0/libs/sort/__build__  
sjr@Mac __build__ % cmake --build . --target tests  
[  5%] Building CXX object test/CMakeFiles/test_spinsort.dir/test_spinsort.cpp.o  
/Users/sjr/boost/boost_1_88_0/libs/sort/test/test_spinsort.cpp:21:10: fatal error: 'boost/test/included/test_exec_monitor.hpp' file not found  
   21 | #include <boost/test/included/test_exec_monitor.hpp>  
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
make[3]: *** [test/CMakeFiles/test_spinsort.dir/test_spinsort.cpp.o] Error 1  
make[2]: *** [test/CMakeFiles/test_spinsort.dir/all] Error 2  
make[1]: *** [test/CMakeFiles/tests.dir/rule] Error 2  
make: *** [tests] Error 2

---

## Comment 6

> Username: pdimov  
> Created at: 2025-06-19 11:59:08 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-2987833685  

You need to issue the commands from the Boost root, not from libs/sort.  
  
If you want to support testing from libs/sort, you can do that too, but you'd need to find the rest of Boost somehow (and only do that when you're the root project so as to not break the other use.)

---

## Comment 7

> Username: spreadsort  
> Created at: 2025-06-19 13:46:37 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-2988169426  

I don't know how to add this support you are asking for.

---

## Comment 8

> Username: pdimov  
> Created at: 2025-06-24 12:36:14 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-3000261796  

I was going to make a pull request, but I'm not sure I understand how your existing test/CMakeLists.txt is supposed to be used. What are the commands that you use - at the moment, using the existing test/CMakeLists.txt - to test the library with CMake?

---

## Comment 9

> Username: spreadsort  
> Created at: 2025-06-26 01:23:15 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-3006711617  

I use b2 test.  I don't use CMake.  Nigel Stewart added it: https://github.com/boostorg/sort/commit/098a6a8c0fcdffcd12fb7bc83290670b0ddc4e9f

---

## Comment 10

> Username: pdimov  
> Created at: 2025-06-28 06:21:39 UTC  
> Url: https://github.com/boostorg/sort/issues/94#issuecomment-3015016137  

See https://github.com/boostorg/sort/pull/95.
