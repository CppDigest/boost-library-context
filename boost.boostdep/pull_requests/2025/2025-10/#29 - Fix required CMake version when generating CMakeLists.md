# #29 Fix required CMake version when generating CMakeLists [Merged]

> Username: Flamefire  
> Created at: 2025-10-02 15:46:45 UTC  
> Updated at: 2025-10-03 11:58:23 UTC  
> Merged at: 2025-10-03 11:38:40 UTC  
> Closed at: 2025-10-03 11:38:40 UTC  
> Url: https://github.com/boostorg/boostdep/pull/29  

We don't need anything lower than 3.8 anymore and higher C++ standards than 11 require higher CMake versions

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-02 16:22:55 UTC  
> Url: https://github.com/boostorg/boostdep/pull/29#issuecomment-3362065154  

I'm not entirely sure that this is what we want here.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-03 10:01:02 UTC  
> Url: https://github.com/boostorg/boostdep/pull/29#issuecomment-3365108263  

In which way?     
If you emit `cmake_minimum_required(VERSION 3.8` and use `cxx_std_20` it will yield a generate failure that is not easy to understand the cause of ("Unknown feature used") and I consider that a bug: The required CMake version stated is wrong  
  
So mapping the version to the right CMake version is just avoiding this bug.  
  
As for dropping CMake 3.5 where possible: Given that BoostRoot now requires CMake 3.8 I don't see any value in using a lower version.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-10-03 10:32:22 UTC  
> Url: https://github.com/boostorg/boostdep/pull/29#issuecomment-3365195198  

Suppose we have a few C++23 libraries in Boost, and the user wants to install Boost using CMake 3.16. I agree that the current state is to issue an error, and that this is undesirable. But I'm not sure that issuing a different, better, error at configure time is particularly useful. This forces the user to care about libraries about which he may not care at all, and to manually exclude them.  
  
I of course do not object to always requiring 3.8.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-10-03 10:45:24 UTC  
> Url: https://github.com/boostorg/boostdep/pull/29#issuecomment-3365227817  

>  current state is to issue an error, and that this is undesirable. But I'm not sure that issuing a different, better, error at configure time is particularly useful.  
  
Why is it not useful to issue a better error if we do have to issue an error in all cases?  
  
> Suppose we have a few C++23 libraries in Boost, and the user wants to install Boost using CMake 3.16. [...] This forces the user to care about libraries about which he may not care at all, and to manually exclude them.  
  
Exactly: He has to exclude all libraries that are not compatible with CMake 3.16 (because they are using C++23) as otherwise he will get an error.  
  
That is the case with and without this PR, the only difference is the place where the error occurs.  
  
Currently the error shows at generate time for the affected libraries **and their dependencies** which makes it not clear which library is the one stopping the build, although of course all have to be excluded anyway.  
  
I found that while opening PRs for CMake 3.8 in libraries where "Unknown cxx_std_11 feature" showed. Boost.Filesystem didn't use it but depended on a library that does.  
  
This change shows a clear error "CMake version too low" for the library that requires it and upon exclusion would show an undeclared target on e.g. Boost.Filesystem referencing a library that was excluded.  
  
IMO this is much better to understand and follow for users.  
  
  
If we really do not want to issue an error if possible we could conditionally exclude libraries based on the declared CMake version as I did in the CI for boostorg/cmake. That of course requires the correct CMake version to be declared in the first place and falls short for dependent libraries.      
Also more-or-less silently not building libraries is IMO worse than an error that can be easily resolved by exclusions or CMake updates

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-10-03 10:52:14 UTC  
> Url: https://github.com/boostorg/boostdep/pull/29#issuecomment-3365244296  

> Why is it not useful to issue a better error if we do have to issue an error in all cases?  
  
I didn't say that at all.

---

## Comment 6

> Username: Flamefire  
> Created_at: 2025-10-03 11:06:18 UTC  
> Url: https://github.com/boostorg/boostdep/pull/29#issuecomment-3365280459  

Then I don't understand what you meant. Could you clarify please?  
  
I'd say this PR is strictly an improvement over the current state, isn't it?      
Avoiding or not the error caused by too low CMake versions for some libraries should be an orthogonal issue and one I don't see as an issue.     
I never really liked the b2-approach of silently dropping libraries when a lower C++ standard is used then they require as when doing (semi-)automated installations you'll notice that only when trying to use them or need to verify after the installation that all libraries are present which is hard to automate as new libraries get added.

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-10-03 11:20:35 UTC  
> Url: https://github.com/boostorg/boostdep/pull/29#issuecomment-3365316295  

> I'd say this PR is strictly an improvement over the current state, isn't it?  
  
It is a strict improvement, yes.  
  
What I had in mind was that, instead of  
```  
cmake_minimum_required(3.20...3.31)  
  
target_compile_features(boost_mylib INTERFACE cxx_std_23)  
```  
we could for example have  
```  
cmake_minimum_required(3.8...3.31)  
  
if("cxx_std_23" IN_LIST CMAKE_CXX_COMPILE_FEATURES)  
  target_compile_features(boost_mylib INTERFACE cxx_std_23)  
else()  
  message(WARNING "...")  
endif()  
```  
although maybe it's not worth the bother. I'll think about it some more.

---

## Comment 8

> Username: Flamefire  
> Created_at: 2025-10-03 11:28:23 UTC  
> Url: https://github.com/boostorg/boostdep/pull/29#issuecomment-3365341872  

> we could for example have  
>   
> ```  
> cmake_minimum_required(3.8...3.31)  
>   
> if("cxx_std_23" IN_LIST CMAKE_CXX_COMPILE_FEATURES)  
>   target_compile_features(boost_mylib INTERFACE cxx_std_23)  
> else()  
>   message(WARNING "...")  
> endif()  
> ```  
  
That would just move the error further away, to compile time, if the compilers default C++ level doesn't happen to be C++23  
  
Of course we can implement something in CMake like `if(compiler supports -std=c++23) target_compile_flags(-std=c++23)` but would then need special cases for e.g. MSVC and c++2b vs c++23 etc.     
Or have the warning instruct the user to add `-std=c++23` (or equivalent) to `CMAKE_CXX_FLAGS` manually if supported  
  
IMO not worth the bother  though

---
