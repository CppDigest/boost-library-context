# #56 - test on windows with appveyor [Closed]

> Username: HDembinski  
> Created at: 2018-07-04 23:29:46 UTC  
> Updated at: 2018-07-18 10:02:32 UTC  
> Closed at: 2018-07-14 17:54:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/56  



---

## Comment 1

> Username: HDembinski  
> Created at: 2018-07-14 18:57:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/56#issuecomment-405043018  

@jbuonagurio It took a huge internal refactoring, but now I can proudly claim that the master now works on Visual Code 14 2015, https://ci.appveyor.com/project/HDembinski/histogram/build/1.0.28. Please let me know whether it works for you. The interface changed with respect to version 2.0, I will make a 3.0 release soon.

---

## Comment 2

> Username: jbuonagurio  
> Created at: 2018-07-17 16:39:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/56#issuecomment-405647677  

@HDembinski I integrated the current master branch into my project and it's working very well. I also ran all tests with MSVC 2017 (19.12.25835, x64). All pass except the (platform-dependent) `sizeof(axis::integer<>)`  in axis_test.cpp. Changing to:  
```  
BOOST_TEST(sizeof(axis::integer<>) <= 56);  
```  
All tests pass.

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-07-18 10:02:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/56#issuecomment-405879523  

Cool, thank you for this excellent feedback! I will remove these tests of sizeof..., because like you said they are platform-dependent. I wanted to track that the sizes of these axis objects don't become too large, because we want them all to fit in the CPU cache, but I guess I have to do it differently.
