# #52 - Optional boost dependencies. [Closed]

> Username: Neumann-A  
> Created at: 2024-03-10 14:43:19 UTC  
> Updated at: 2024-04-24 10:23:55 UTC  
> Closed at: 2024-04-24 10:23:55 UTC  
> Url: https://github.com/boostorg/cmake/issues/52  

The CMake build of boost is a bit overeager declaring dependencies. E.g. boost-math completely depends on boost-random making the use of the rest of boost-math impossible if boost-random is not available/buildable on a platform. Considering that boost-math is a header only library this seems excessive considering that the b2 build requires non of those dependencies.    
  
I suggest to use `$<TARGET_NAME_IF_EXISTS:...>` for optional/non-vital dependencies and mark the `find_dependency` call as optional. Furthermore there could be preprocessor defines to disable parts of the headers to inform the user about the issue if a header is used which requires a dependency.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-19 23:50:24 UTC  
> Url: https://github.com/boostorg/cmake/issues/52#issuecomment-2067401937  

The CMake build requires linking to the target even when only a header is included, in order for the proper include path (`libs/<libname>/include`) to be supplied. The b2 build does not, because it assumes that all headers are copied to a single directory beforehand.

---

## Comment 2

> Username: Neumann-A  
> Created at: 2024-04-20 06:46:55 UTC  
> Url: https://github.com/boostorg/cmake/issues/52#issuecomment-2067579438  

> The b2 build does not, because it assumes that all headers are copied to a single directory beforehand.  
  
So basically if that assumption is broken the b2 build is officially toast but the parts you actually want to use still work due to that assumption.  
  
> The CMake build requires linking to the target even when only a header is included, in order for the proper include path (libs/<libname>/include) to be supplied.  
  
I disagree: It only requires linking if it is an actual build requirement or a header target which is basically included everywhere. However there are headers/deps which are not a strict requirement, e.g. boost-math depends on boost-random however you only need the boost-random dependency if you are using the parts of boost-math which actually requires it. As such you can mark boost-random as an optional dependency and let the user decide to pull it in or not or always pull it in when it is available.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-04-20 07:34:09 UTC  
> Url: https://github.com/boostorg/cmake/issues/52#issuecomment-2067590955  

I don't see what you disagree with. If the proper -I flag is not supplied, including headers doesn't work. Your disagreement doesn't make it work.

---

## Comment 4

> Username: Neumann-A  
> Created at: 2024-04-20 13:04:14 UTC  
> Url: https://github.com/boostorg/cmake/issues/52#issuecomment-2067667292  

> If the proper -I flag is not supplied, including headers doesn't work.   
  
That is only true if the headers are actually required by the users. headers not included anywhere don't need to be looked up ;)   
Lets iterate the problem with boost-math in a bit more detail.  
boost-math depends on boost-random. boost-random does not compile for UWP due to using restricted functions call.   
As such boost-math is not available for uwp since the boost-random dep cannot be fulfilled. However, if you look where boost-random is actually necessary in boost-math you will notice that it is only a very small subset of boost-math is actually requiring it. Having a hard dependency on boost-random however excludes the usage of the rest of boost-math completely (the uwp example is just one I encountered, there are probably more)  
  
For me `$<TARGET_NAME_IF_EXISTS:...>` is the most user friendly way to opt into headers which are not a hard requirement for usage. Additionally, it does not require a lot of rewriting how the boost cmake build works.    
  
The alternative would be to identify all the subparts of boost modules which can be considered optional and guard them via   
 a lookup of `BOOST_INCLUDE_LIBRARIES/BOOST_EXCLUDE_LIBRARIES` and only install the headers which actually work with the current set of available boost libs.   
The b2 way of doing stuff does not magically make boost-random work on uwp just because the headers are available ....   
  
another alternative is to introduce a check/genex like: `$<$<BOOL:BOOST_MATH_WITH_RANDOM>:...>` or similar to declare optional features/dependencies of a boost module.   
  
(I don't actually know why boost-cmake does a parsing of the submodules CMakeLists.txt; It could simply look at the targets after they were created and then pull in other required boost libs. This would make optional inter boost deps a lot easier to handle.)

---

## Comment 5

> Username: pdimov  
> Created at: 2024-04-20 14:05:42 UTC  
> Url: https://github.com/boostorg/cmake/issues/52#issuecomment-2067684638  

Is there a Boost.Math issue tracking this? I'd like to look at the discussion there.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-04-20 14:09:33 UTC  
> Url: https://github.com/boostorg/cmake/issues/52#issuecomment-2067685624  

> (I don't actually know why boost-cmake does a parsing of the submodules CMakeLists.txt; It could simply look at the targets after they were created and then pull in other required boost libs. This would make optional inter boost deps a lot easier to handle.)  
  
I'm not sure how this would simplify matters (if using generator expressions, which will only be evaluated at generate time.)  
  
I suppose it could help if you do something like  
```  
if(BOOST_MYLIB_WITH_RANDOM)  
    target_link_libraries(boost_mylib PUBLIC Boost::random)  
endif()  
```  
which will currently be picked up unconditionally as a dependency.

---

## Comment 7

> Username: pdimov  
> Created at: 2024-04-20 14:11:27 UTC  
> Url: https://github.com/boostorg/cmake/issues/52#issuecomment-2067686187  

Either way though, I intensely dislike "optional" dependencies and would like to support them as little as possible.  
  
I can probably accept some minimal support in the form of recognizing `$<TARGET_NAME_IF_EXISTS:...>` as suggested in your PR, provided this proves workable in practice for e.g. the Math issue.
