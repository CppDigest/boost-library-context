# #742 - CMake: Dependency on Boost::filesystem is wrong [Closed]

> Username: Neumann-A  
> Created at: 2024-03-10 14:55:48 UTC  
> Updated at: 2024-09-27 07:01:20 UTC  
> Closed at: 2024-09-27 07:00:59 UTC  
> Url: https://github.com/boostorg/gil/issues/742  

The CMake files for boost-gil declare a dependency on Boost::filesystem. However, that dependency is guarded by `BOOST_GIL_IO_USE_BOOST_FILESYSTEM` in the headers and there is no code in the CMake files which ever sets that preprocessor define. As such the dependency on Boost::filesystem is wrong or there needs to be a cmake option adding that dependency  and the preprocessor define.

---

## Comment 1

> Username: sdebionne  
> Created at: 2024-03-14 07:39:14 UTC  
> Url: https://github.com/boostorg/gil/issues/742#issuecomment-1996746895  

Thank you for reporting this. Indeed we could add a CMake option (which default value would depend on the cpp standard) to remove a dependency that is not really needed in C++17 (unless `BOOST_GIL_IO_USE_BOOST_FILESYSTEM` is defined).

---

## Comment 2

> Username: simmplecoder  
> Created at: 2024-04-02 21:20:02 UTC  
> Url: https://github.com/boostorg/gil/issues/742#issuecomment-2033119316  

@Neumann-A I will try to get a commit in this week, next week it should be implemented. Do you have some easy test case I could throw into the commit or should I try to write my own?

---

## Comment 3

> Username: Neumann-A  
> Created at: 2024-04-02 21:24:13 UTC  
> Url: https://github.com/boostorg/gil/issues/742#issuecomment-2033124812  

No I don't have a test case.

---

## Comment 4

> Username: simmplecoder  
> Created at: 2024-04-12 21:05:36 UTC  
> Url: https://github.com/boostorg/gil/issues/742#issuecomment-2052531991  

Sorry last days at work were very hectic. I have created a draft PR https://github.com/boostorg/gil/pull/743, I am a bit unsure how to create a test for it yet, but after I figure it out I will request a review. If you would like to test it out, you can pull my branch and set the following:  
  
```  
-DBOOST_GIL_USE_BOOST_FILESYSTEM=OFF  
-DCMAKE_CXX_STANDARD=17   
```  
  
C++ standard can be anything equal or higher to 17.

---

## Comment 5

> Username: mloskot  
> Created at: 2024-04-16 07:14:23 UTC  
> Url: https://github.com/boostorg/gil/issues/742#issuecomment-2058395055  

@simmplecoder I wouldn't worry about testing this with the already cases-overloaded CI. It would be great if one two people could try it out and confirm though.

---

## Comment 6

> Username: mloskot  
> Created at: 2024-09-27 07:00:59 UTC  
> Url: https://github.com/boostorg/gil/issues/742#issuecomment-2378542322  

Done in   
- #743
