# #181 - Test libraries and executables should be declared EXCLUDE_FROM_ALL [Closed]

> Username: pdimov  
> Created at: 2024-05-14 18:11:40 UTC  
> Updated at: 2024-05-16 17:33:17 UTC  
> Closed at: 2024-05-16 17:33:17 UTC  
> Url: https://github.com/boostorg/nowide/issues/181  

The Boost convention is that `cmake --build .` only builds the libraries. Test libraries and executables are built with `cmake --build . --target tests`.

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-05-14 19:03:08 UTC  
> Url: https://github.com/boostorg/nowide/issues/181#issuecomment-2110948300  

Is it? I was only aware of this idiom:  
```  
if(BUILD_TESTING)  
  add_subdirectory(test)  
endif()  
```  
  
As that is what is usually done with `cmake --build .` or `make (all)`: Build everything (unless disabled). Or am I mistaken?

---

## Comment 2

> Username: pdimov  
> Created at: 2024-05-14 19:12:11 UTC  
> Url: https://github.com/boostorg/nowide/issues/181#issuecomment-2110968427  

It is.  
  
Ideally, we should even have fine-grained, per library, test targets, e.g. `cmake --build . --target boost_nowide_tests` or similar, although I haven't yet nailed this down or implemented it.  
  
While in b2 you can say `b2 libs/nowide/test`, and this will build the test executables of Nowide with their dependencies, and run them, there's no equivalent in CMake, so we have to do that ourselves, somehow.
