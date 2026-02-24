# #23 - It would be useful to have a section on how to run the test [Closed]

> Username: viboes  
> Created at: 2015-10-04 00:33:10 UTC  
> Updated at: 2016-01-12 15:10:01 UTC  
> Closed at: 2016-01-12 15:10:01 UTC  
> Url: https://github.com/boostorg/hof/issues/23  



---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 04:04:24 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-145425110  

The test can be run by building the `check` target. So either doing `make check` if you are using make, or just doing `cmake --build build --target check`(assuming you build directory is `build`) for any build system.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:56:11 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-145692069  

Please could you add a section.  
  
I have cmake 2.8.12.2 and I'm getting  
  
```  
new-host:Fit viboes$ cmake --build build --target check  
Error: could not load cache  
new-host:Fit viboes$ cmake  
cmake version 2.8.12.2  
```  
  
Is this error due to the fact that you require cmake 3. ?

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 23:27:10 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-145697300  

I dont know. What is the output of the running cmake the first time?

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-06 00:00:44 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-145701680  

It is not the first time. I use it with other projects. Do we need version cmake 3.0 or you have tested only with this version?

---

## Comment 5

> Username: bcachet  
> Created at: 2015-10-07 08:50:59 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-146121290  

`couldn't load the cache` means CMake hasn't been run to generate the build artifact.  
  
To run the tests you have to:  
  
``` bash  
mkdir build  
cd build  
cmake ..  
cd ..  
cmake --build build --target check  
```

---

## Comment 6

> Username: viboes  
> Created at: 2015-10-07 17:19:58 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-146267740  

Sorry, I don't use to use cmake.  
  
I've changed the cmake version to 2.8 and I'm getting compiler errors after   
  
```  
cmake --build build --target check  
```  
  
How I can choose the compiler to use?

---

## Comment 7

> Username: viboes  
> Created at: 2015-10-07 17:42:20 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-146273744  

I have reached to select the compiler and almost everything is working.   
I'm using gcc-5.1 c++11 or c++14.  
  
I'm getting this error however  
  
```  
In file included from /Users/viboes/github/Fit/test/capture.cpp:1:0:  
/Users/viboes/github/Fit/test/capture.cpp: In member function ‘void test_6::operator()() const’:  
/Users/viboes/github/Fit/test/test.h:79:36: erreur: non-constant condition for static assertion  
 #define FIT_STATIC_TEST_CHECK(...) static_assert(__VA_ARGS__, FIT_STRINGIZE(__VA_ARGS__))  
                                    ^  
/Users/viboes/github/Fit/test/capture.cpp:8:5: note: in expansion of macro ‘FIT_STATIC_TEST_CHECK’  
     FIT_STATIC_TEST_CHECK(fit::capture(1, 2)(binary_class())() == 3);  
```  
  
and other similar  
  
```  
The following tests FAILED:  
      6 - capture (Not Run)  
      7 - combine (Not Run)  
     16 - function (Not Run)  
     21 - infix (Not Run)  
     29 - partial (Not Run)  
     30 - pipable (Not Run)  
```  
  
Do you have an idea what is going wrong?

---

## Comment 8

> Username: bcachet  
> Created at: 2015-10-07 18:31:01 UTC  
> Updated at: 2015-10-07 18:37:19 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-146286450  

If you want to select a new compiler, you have to generate new build artifact. That means that you should almost always clean the initial destination of your build artifacts (CMake will detect that you try to build with a different compiler and will inform you that you should first clean or run this from a different directory)  
  
```  
rm -rf build  
mkdir -p build  
cd build  
CXX=clang++ cmake ..  
cd ..  
cmake --build build --target check  
```  
  
Another option is to have one build directory per compiler you plan to test  
  
```  
for COMPILER in clang++ g++-5; do  
  mkdir build-${COMPILER}  
  cd build-${COMPILER}  
  CXX=${COMPILER} cmake ..  
  cd ..  
  cmake --build build-${COMPILER} --target check  
done  
```

---

## Comment 9

> Username: bcachet  
> Created at: 2015-10-07 18:46:44 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-146290949  

@viboes I have tested with success both clang-700.0.72 and gcc 5.2.0

---

## Comment 10

> Username: viboes  
> Created at: 2015-10-07 20:47:53 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-146322896  

@bcachet Thanks a lot, but even after rm I get the same error.

---

## Comment 11

> Username: pfultz2  
> Created at: 2015-10-07 20:54:42 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-146325020  

I just add gcc 5.1 to the travis CI. It looks like it fails. I am going to look into this.

---

## Comment 12

> Username: pfultz2  
> Created at: 2015-10-07 22:08:52 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-146346926  

Well it looks like it is related to this bug report [here](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=65896), which was fixed in gcc 5.2. I haven't found a good workaround for it yet.

---

## Comment 13

> Username: pfultz2  
> Created at: 2015-10-20 17:01:27 UTC  
> Url: https://github.com/boostorg/hof/issues/23#issuecomment-149633371  

Well it looks like gcc 5.1 won't be supported at all, it just has too many problems. Also, right now, I haven't found a way to add gcc 5.2 to Travis, either. So i am not regularly running the tests for gcc 5.2.
