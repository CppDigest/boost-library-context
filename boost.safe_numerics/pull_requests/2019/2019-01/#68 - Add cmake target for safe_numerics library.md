# #68 [CMake] Add cmake target for safe_numerics library [Merged]

> Username: Mike-Devel  
> Created at: 2019-01-05 08:59:54 UTC  
> Updated at: 2019-05-03 20:35:58 UTC  
> Merged at: 2019-05-03 17:04:51 UTC  
> Closed at: 2019-05-03 17:04:51 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/68  

Follows modern best practices (modify target properties instead of global ones) and makes safe_numerics easier/more ideomatic to use with the `add_subdirectory( <path-to--lib> ) ` workflow.

---

## Comment 1

> Username: robertramey  
> Created_at: 2019-05-03 17:26:32 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/68#issuecomment-489175181  

Hmmm - I merged this in and it's now breaking my build with  
  
CMake Error in example/CMakeLists.txt:  
  Specified unknown feature "cxx_std_14" for target "example1".  
  
Did you actually test this?  What do I have to do to fix this?

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-05-03 19:55:24 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/68#issuecomment-489220564  

Sorry,  completely forgot about that PR. Yes I did test it,  but only locally. What cmake version was used? I think the cxx_std_14 feature requires 3.8 or later.  
  
I have to admit that I'm nowerdays on the fence using those cxx_std_XX is really a good idea.

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2019-05-03 19:56:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/68#issuecomment-489220959  

Shouldn't the ci have catched the error before it got merged?

---

## Comment 4

> Username: robertramey  
> Created_at: 2019-05-03 20:21:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/68#issuecomment-489227590  

OK   
a) I found that the cxx_std_14 feature requires 3.8 or later.  
b) still doesn't find the include files.  I'm building with Xcode.  I've managed to get it compiler by fixing up the generated Xcode project by hand. I don't remember whether I always had to do this or it's a new thing.  
c) Found it.  Needed to add:  
target_include_directories(boost_safe_numerics INTERFACE "${Boost_INCLUDE_DIRS}")  
d) It's possible that Xcode needs this while running from the command line doesn't.  In any case, looks like I'm out of the woods.  
e) Note: in the future don't for get to update the first statement:  
cmake_minimum_required(VERSION 3.8.6)  
to include the new minimum version #. This would have been helpful.  
  
Thanks for taking an interest in enhancing this library  
  
Robert Ramey

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2019-05-03 20:35:58 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/68#issuecomment-489231622  

Sorry for the trouble

---
