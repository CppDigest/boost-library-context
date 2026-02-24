# #614 - CMake superproject tests are failing because of Math [Closed]

> Username: pdimov  
> Created at: 2021-04-25 18:20:10 UTC  
> Updated at: 2024-02-22 10:34:51 UTC  
> Closed at: 2024-02-22 10:34:51 UTC  
> Url: https://github.com/boostorg/math/issues/614  

```  
Scanning dependencies of target compile_tests  
[ 17%] Building CXX object libs/math/test/compile_test/CMakeFiles/compile_tests.dir/compl_abs_incl_test.cpp.o  
/home/travis/build/boostorg/boost/libs/math/test/compile_test/compl_abs_incl_test.cpp:9:39: fatal error: boost/math/complex/fabs.hpp: No such file or directory  
compilation terminated.  
libs/math/test/compile_test/CMakeFiles/compile_tests.dir/build.make:62: recipe for target 'libs/math/test/compile_test/CMakeFiles/compile_tests.dir/compl_abs_incl_test.cpp.o' failed  
make[2]: *** [libs/math/test/compile_test/CMakeFiles/compile_tests.dir/compl_abs_incl_test.cpp.o] Error 1  
CMakeFiles/Makefile2:14516: recipe for target 'libs/math/test/compile_test/CMakeFiles/compile_tests.dir/all' failed  
make[1]: *** [libs/math/test/compile_test/CMakeFiles/compile_tests.dir/all] Error 2  
Makefile:140: recipe for target 'all' failed  
make: *** [all] Error 2  
The command "cmake --build ." exited with 2.  
```  
https://travis-ci.org/github/boostorg/boost/jobs/768322753  
  
In addition, the `compile_tests` target needs to be prefixed with `boost_math-` by convention; CMake targets are global.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-05-02 17:05:03 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-830839703  

@mborland , @pdimov is this now fixed?

---

## Comment 2

> Username: mborland  
> Created at: 2021-05-02 17:09:55 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-830840374  

The CMake build does not make it to math anymore so I am not sure. [Currently fails at Boost.JSON.](https://travis-ci.org/github/boostorg/boost/jobs/769225032#L1226)

---

## Comment 3

> Username: pdimov  
> Created at: 2021-05-02 17:25:18 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-830842497  

I reported this failure to Boost.JSON. In the meantime you can check that Math works by  
```  
mkdir __build; cd __build  
cmake -DBOOST_ENABLE_CMAKE=ON -DBUILD_TESTING=ON -DBOOST_INCLUDE_LIBRARIES=math ..  
cmake --build .  
```  
This gives me a bunch of warnings  
```  
C:\boost-git\develop\libs\math\include\boost/math/tools/is_standalone.hpp(12,1): warning C4005: 'BOOST_MATH_STANDALONE'  
: macro redefinition [C:\boost-git\develop\__build\libs\math\test\compile_test\boost_math-compile_tests.vcxproj]  
C:\boost-git\develop\libs\math\test\compile_test\compl_abs_incl_test.cpp : message : see previous definition of 'BOOST_  
MATH_STANDALONE' [C:\boost-git\develop\__build\libs\math\test\compile_test\boost_math-compile_tests.vcxproj]  
```  
and so far one error  
```  
C:\boost-git\develop\libs\math\include\boost/math/interpolators/detail/cardinal_trigonometric_detail.hpp(11,10): fatal  
error C1083: Cannot open include file: 'fftw3.h': No such file or directory [C:\boost-git\develop\__build\libs\math\tes  
t\compile_test\boost_math-compile_tests.vcxproj]  
```

---

## Comment 4

> Username: pdimov  
> Created at: 2021-05-02 17:29:24 UTC  
> Updated at: 2021-05-02 17:30:29 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-830843011  

There's also  
```  
C:\boost-git\develop\libs\math\test\compile_test\test_compile_result.hpp(57,51): error C2466: cannot allocate an array  
of constant size 0 [C:\boost-git\develop\__build\libs\math\test\compile_test\boost_math-compile_tests.vcxproj]  
C:\boost-git\develop\libs\math\test\compile_test\test_compile_result.hpp(68): message : see reference to function templ  
ate instantiation 'void check_result_imp<T1,T2>(T1,T2)' being compiled [C:\boost-git\develop\__build\libs\math\test\com  
pile_test\boost_math-compile_tests.vcxproj]  
          with  
          [  
              T1=int,  
              T2=float  
          ]  
C:\boost-git\develop\libs\math\test\compile_test\sf_rsqrt_incl_test.cpp(18): message : see reference to function templa  
te instantiation 'void check_result<int,Real>(T2)' being compiled [C:\boost-git\develop\__build\libs\math\test\compile_  
test\boost_math-compile_tests.vcxproj]  
          with  
          [  
              Real=float,  
              T2=float  
          ]  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2021-05-02 17:38:27 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-830844234  

Doing the `compile` tests at build time isn't ideal, but CMake's testing architecture doesn't make it convenient to defer them to the testing phase. There's a known technique of doing that by launching a nested `cmake` as part of the test, see f.ex. how https://github.com/boostorg/cmake/blob/develop/include/BoostTest.cmake implements compile tests. The downside is that parallel testing doesn't work correctly.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-05-06 17:17:17 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-833699665  

Boost.JSON is finally fixed, so now the CMake tests get to Math: https://travis-ci.org/github/boostorg/boost/jobs/769691555

---

## Comment 7

> Username: pdimov  
> Created at: 2021-05-06 17:22:19 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-833702810  

The error seems to imply that the tests aren't done in the appropriate language mode (I suppose Math needs C++14?)

---

## Comment 8

> Username: pdimov  
> Created at: 2021-05-06 17:24:26 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-833704211  

I don't think this https://github.com/boostorg/math/blob/0cd6039cb19986ba842875f10faf700b9f9eb3bd/CMakeLists.txt#L19 is correct. For one, it causes redefinition warnings. For another, within the superproject Math shouldn't be in standalone mode.

---

## Comment 9

> Username: pdimov  
> Created at: 2021-05-06 17:27:53 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-833706595  

C++17 is enabled here https://github.com/boostorg/math/blob/0cd6039cb19986ba842875f10faf700b9f9eb3bd/test/compile_test/CMakeLists.txt#L7 but I'm not sure it does anything on g++ 5. :-)

---

## Comment 10

> Username: pdimov  
> Created at: 2021-05-06 18:16:40 UTC  
> Updated at: 2021-05-06 18:17:44 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-833751254  

I suspect you want something like this:  
```  
if(BOOST_SUPERPROJECT_VERSION)  
  set(BOOST_MATH_STANDALONE OFF)  
else()  
  option(BOOST_MATH_STANDALONE "Use Boost.Math standalone" ON)  
endif()  
  
if(BOOST_MATH_STANDALONE)  
  target_compile_definitions(boost_math PUBLIC "BOOST_MATH_STANDALONE=1")  
else()  
  target_link_libraries(boost_math  
    ...  
  )  
endif()  
```

---

## Comment 11

> Username: pdimov  
> Created at: 2021-05-27 16:42:35 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-849781437  

Are there plans of fixing the CMakeLists.txt files? The main one has two syntax issues and the semantics aren't quite right either. In https://github.com/boostorg/math/blob/90278fcf9ef0da8fac43a1dfac05a28c37c1cded/CMakeLists.txt#L17, `EXISTS` checks for the existence of a file, which isn't right; and in https://github.com/boostorg/math/blob/90278fcf9ef0da8fac43a1dfac05a28c37c1cded/CMakeLists.txt#L22, `!BOOST_MATH_STANDALONE` is the string `"!BOOST_MATH_STANDALONE"`, which tests true. (In CMake the correct syntax is `NOT BOOST_MATH_STANDALONE`, but this CMake variable is never defined anyway.)  
  
In addition, `BOOST_MATH_STANDALONE` is also defined by `is_standalone.hpp`:  
```  
/home/travis/build/boostorg/boost/libs/math/include/boost/math/tools/is_standalone.hpp:12:0: warning: "BOOST_MATH_STANDALONE" redefined  
```  
which is also a problem; Math shouldn't be in standalone mode when inside a Boost distribution.  
  
What is the intended behavior here?

---

## Comment 12

> Username: jzmaddock  
> Created at: 2021-05-27 16:47:53 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-849784813  

I am as you know a CMake ignoramus :(  
  
@mborland can we fix this?  
  
The intention, was that this CMake file be completely separate from anything else in Boost, and test this lib in standalone mode.  We don't want to use Boost.Build for this as it means pulling in more of Boost which sort of defeats the point of the test.  Can a nested CMake file be completely isolated from one in a parent directory?

---

## Comment 13

> Username: pdimov  
> Created at: 2021-05-27 17:00:34 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-849792885  

Yes, all CMake files are isolated from one another by default. There's no implied relationship as with the Jamfiles that scan upward.  
  
It's not possible for libs/math/CMakeLists.txt to be separate from anything else in Boost, because it's part of the Boost CMake build. But it's possible to make it serve both purposes.  
  
Either way the fact that `is_standalone.hpp` autodetects standalone mode is bothersome. And the only reason we know about it is because CMakeLists.txt incorrectly defines BOOST_MATH_STANDALONE so we get the redefinition warning. If it didn't, is_standalone would have silently enabled standalone mode and nobody would have been any wiser.  
  
I can try to fix your CMakeLists.txt files the way I think they ought to work?

---

## Comment 14

> Username: pdimov  
> Created at: 2021-05-27 17:22:30 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-849806517  

It might be better to make BOOST_MATH_STANDALONE take values of 0 or 1, and when it's defined, disable the autodetection in `is_standalone.hpp`. This way the user can force standalone (or not) mode and avoid the autodetection.

---

## Comment 15

> Username: pdimov  
> Created at: 2021-05-27 17:25:16 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-849808266  

https://github.com/boostorg/math/commit/c83a5644a097e5e3bc641f6c4ff27ef98f797a9a: What this does is add a CMake option for standalone mode, which is ON by default when outside the Boost superproject, and OFF and hidden when inside.

---

## Comment 16

> Username: pdimov  
> Created at: 2021-05-27 17:31:41 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-849812228  

https://github.com/boostorg/math/commit/20de153ca35b28e6b6fe97007a270b97ef513f0d: This links the tests to the Boost::math library, instead of adding include directories by hand.

---

## Comment 17

> Username: pdimov  
> Created at: 2021-05-27 17:33:32 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-849813356  

If you want the test suite to only be for your internal use, and not run as part of the Boost CMake test run, and (probably) not run when Math is used by a user project with add_subdirectory, I suppose the right thing to do is to only activate it when Math is the root project.

---

## Comment 18

> Username: pdimov  
> Created at: 2021-05-27 17:39:01 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-849816673  

https://github.com/boostorg/math/commit/a1d76a4fc3df9823a5ce7bfe2f3f8632f05ac644: does the above.

---

## Comment 19

> Username: mborland  
> Created at: 2021-05-27 17:52:47 UTC  
> Url: https://github.com/boostorg/math/issues/614#issuecomment-849824903  

> I am as you know a CMake ignoramus :(  
>   
> @mborland can we fix this?  
>   
> The intention, was that this CMake file be completely separate from anything else in Boost, and test this lib in standalone mode.  We don't want to use Boost.Build for this as it means pulling in more of Boost which sort of defeats the point of the test.  Can a nested CMake file be completely isolated from one in a parent directory?  
  
This was something on my list of items to fix now that the fixes for the standalone tests have been merged. Unfortunately I am in the midst of another international relocation so my recommendation would be if this needs to be fixed quickly @pdimov commits be applied.
