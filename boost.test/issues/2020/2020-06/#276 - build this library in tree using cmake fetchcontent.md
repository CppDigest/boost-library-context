# #276 - build this library in tree using cmake fetchcontent [Open]

> Username: Char-Aznable  
> Created at: 2020-06-20 03:50:40 UTC  
> Updated at: 2022-03-05 22:37:58 UTC  
> Url: https://github.com/boostorg/test/issues/276  

Hi, I see there is a CMakeLists.txt in this repo and I have heard an on-going effort in the Boost community to modularize the individual boost packages. What is the current status of building boost::unit_test_framework in cmake as an independent package from the rest of boost? My use case is using cmake fetchcontent from this github to include this library into the build tree of different projects, some use header only part and some use dynamic-linked library. Is this possible now with cmake?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-03-05 22:10:52 UTC  
> Url: https://github.com/boostorg/test/issues/276#issuecomment-1059839368  

I've been busy in 2020 trying to get rid of the rest of Boost to compile Boost.Test, but this is very hard and messing bad with the code. As I understand the ticket, you're fine in having just Boost.Test with CMake's ability to fetch the needed additional dependencies. Is that correct?  
  
If so, I believe I can work on this.

---

## Comment 2

> Username: Char-Aznable  
> Created at: 2022-03-05 22:37:58 UTC  
> Url: https://github.com/boostorg/test/issues/276#issuecomment-1059843763  

>As I understand the ticket, you're fine in having just Boost.Test with CMake's ability to fetch the needed additional dependencies. Is that correct?  
  
Actually no. What I wanted is to have Boost.Test to be built in-tree with my project using CMake's fetchcontent, i.e., cmake fetch this github repo during config time and build Boost.Test as my project's dependency. I don't need other components of Boost.   
  
Thanks for looking into this!
