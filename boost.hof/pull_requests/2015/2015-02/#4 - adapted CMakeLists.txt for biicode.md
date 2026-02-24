# #4 adapted CMakeLists.txt for biicode [Merged]

> Username: drodri  
> Created at: 2015-02-19 00:53:52 UTC  
> Updated at: 2015-02-22 21:03:47 UTC  
> Merged at: 2015-02-19 15:35:41 UTC  
> Closed at: 2015-02-19 15:35:41 UTC  
> Url: https://github.com/boostorg/hof/pull/4  

Hi Paul,  
  
If I have understood your requirements correctly from last email, I think this PR might solve the issue:  
- Moved down the IF(BIICODE) section, so all the above conf is common to both biicode and non-biicode builds  
- Macro Add_test_executable() also works for biicode, where it is not necessary to define the target as it is already defined by biicode, so just the EXCLUDE_FROM_ALL is defined  
- The biicode section calls the macro for every defined EXE. Note that this will work OK even when depending on the lib and the tests are not retrieved  
- It is not necessary to further process the file after that, so for biicode builds a return() finishes the configuration there.  
  
I have tested and the "bii cpp:build --target check" works ok (tested in Win-MInGW) and also "bii cpp:build" does not build the tests.  
The lib as a problem as is. When published and dependen on, it will generate compile errors, as the consumer does not adequately define C++11/14 configuration. I have checked to publish it to one of my tracks and after depend on it, and had to copy all the ENABLE_CXXFLAGS_TO_CHECK stuff in the consumer.  
  
This can be done transitively (only fit defines the flags and the consumer inherits them automatically), if you need help, I might try to contribute with another PR.

---

## Comment 1

> Username: pfultz2  
> Created_at: 2015-02-19 15:36:13 UTC  
> Url: https://github.com/boostorg/hof/pull/4#issuecomment-75072625  

> This can be done transitively (only fit defines the flags and the consumer inherits them automatically), if you need help, I might try to contribute with another PR.  
  
How can it be defined transitively?

---

## Comment 2

> Username: drodri  
> Created_at: 2015-02-19 16:05:30 UTC  
> Url: https://github.com/boostorg/hof/pull/4#issuecomment-75078611  

It is a recommended CMake feature. Instead of just defining the global CXX_FLAGS, properties in general in CMake can be asigned to targets instead including also if you want other targets depending on it to inherit them or not.  
  
Eg: You have target "target1" which headers are in a certain path. If those headers are public and will be included by consumers, those consumers must also define those include directories. So CMake allows to do:  
  
target_include_directories(target1 PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/some_include_dir)  
  
Then, other targets that target_link_libraries or depend on target1, will automatically have that include_dir defined (-I) as include path to search for includes.  
  
Another example, with build options:  
  
target_compile_definitions(${BII_LIB_TARGET} PUBLIC -DWIN32  
                                                        -DDLL_EXPORT  
                                                        # NB: May require tweaking for highly connected applications.  
                                                        -DFD_SETSIZE=1024  
                                                        -D_CRT_SECURE_NO_WARNINGS)  
  
If the target is header only, then it will build no library, and it is just an INTERFACE lib, so write INTERFACE instead of PUBLIC.  Please tell me, if you have problems, I can try to help too with another PR.

---

## Comment 3

> Username: pfultz2  
> Created_at: 2015-02-20 03:14:03 UTC  
> Url: https://github.com/boostorg/hof/pull/4#issuecomment-75183287  

OK thanks for the help. I believe, I got it working, unless there is something else I am missing.

---

## Comment 4

> Username: drodri  
> Created_at: 2015-02-20 09:22:09 UTC  
> Url: https://github.com/boostorg/hof/pull/4#issuecomment-75209964  

Depends on what you want. I would prefer that my consumers can just write the #include and get everything working, which means to make transitive the C++11/14 flags, and it seems right now it is not. So you consumers will have to copy&paste part of your CMakeLists.txt to make it work.   
  
I will try to contribute later with another PR and example that shows it. Cheers.

---

## Comment 5

> Username: pfultz2  
> Created_at: 2015-02-20 17:23:11 UTC  
> Url: https://github.com/boostorg/hof/pull/4#issuecomment-75279769  

> Depends on what you want. I would prefer that my consumers can just write the #include and get everything working  
  
Thats what I was going for. I thought by using `target_compile_definitions` that would work.

---

## Comment 6

> Username: drodri  
> Created_at: 2015-02-20 17:42:56 UTC  
> Url: https://github.com/boostorg/hof/pull/4#issuecomment-75285172  

Yes, I see.   
  
The problem is that you havent published last changes to biicode, and I was reading biicode last version, which cannot be reused yet.  
  
The  
  
target_compile_options(${TEST_NAME} PUBLIC ${CXX_EXTRA_FLAGS})  
  
might not be needed, as you are already in that block.

---

## Comment 7

> Username: pfultz2  
> Created_at: 2015-02-20 17:48:04 UTC  
> Url: https://github.com/boostorg/hof/pull/4#issuecomment-75286041  

> The problem is that you havent published last changes to biicode  
  
Ok got it. I published it now.  
  
> might not be needed, as you are already in that block.  
  
That line is only added when you are not using bii code.

---

## Comment 8

> Username: drodri  
> Created_at: 2015-02-22 21:03:47 UTC  
> Url: https://github.com/boostorg/hof/pull/4#issuecomment-75457768  

You are right, tried it out, works perfectly, thanks!

---
