# #35 - visibility=hidden by default [Closed]

> Username: huangqinjin  
> Created at: 2023-01-08 06:07:07 UTC  
> Updated at: 2023-02-07 17:04:28 UTC  
> Closed at: 2023-02-07 17:04:28 UTC  
> Url: https://github.com/boostorg/cmake/issues/35  

`b2` enables hidden visibility by default.  
https://github.com/boostorg/boost/blob/6d9f09bb168a735c69a291cf9601790e0e840b14/Jamroot#L191  
  
`boost-cmake` should also enable by default:  
```  
set(CMAKE_VISIBILITY_INLINES_HIDDEN ON)  
set(CMAKE_C_VISIBILITY_PRESET hidden)  
set(CMAKE_CXX_VISIBILITY_PRESET hidden)  
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-01-08 15:06:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/35#issuecomment-1374857622  

I think this makes sense. However they should really be the default only, i.e. guard them with `if(DEFINED` and maybe provide a `BOOST_*` variable to toggle inline and preset visibility with one switch. E.g. a bool `BOOST_VISIBILITY_HIDDEN` which when set will set the 2 variables (`CMAKE_C_VISIBILITY_PRESET` is not required for Boost) appropriately and if `BOOST_VISIBILITY_HIDDEN` is not set it will set the 2 variables if they are not set.  
  
Hint for implementors: while `option` would be a good idea I remember an issue with some CMake versions and/or policies where a value set on the commandline via `-D` [would be ignored](https://cmake.org/cmake/help/latest/policy/CMP0077.html#policy:CMP0077) (overwritten by `option`) unless the type is also given on the commandline which is very surprising behavior so that has to be considered/avoided. Also `option` would remove the possibility of the "tri-state" solution as it will be always set.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-01-08 17:47:05 UTC  
> Url: https://github.com/boostorg/cmake/issues/35#issuecomment-1374890061  

OK, let's give it a go.

---

## Comment 3

> Username: Flamefire  
> Created at: 2023-01-09 09:07:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/35#issuecomment-1375299327  

As for https://github.com/boostorg/cmake/commit/0e34b571cb1a8b310c5e2e9727cdcb03d4f5ff2d and my previous comment regarding `option` I made a little test:  
  
```  
cmake_minimum_required(VERSION 3.5...3.16)  
project(Boost VERSION 1.81.0 LANGUAGES CXX)  
  
set(FOO OFF)  
   
option(FOO "DD" ON)  
message(FATAL_ERROR "FOO ${FOO}")  
```  
  
I.e. assuming that some project sets it as a non-cache variable.  
  
- CMake 3.12: First run ON, 2nd run OFF --> Worst case IMO  
- CMake 3.13: All runs OFF  
  
With `-DFOO=ON`: Ignored on CMake 3.13+  
- CMake 3.12: All runs ON  
- CMake 3.13: All runs OFF  
  
With `-DFOO:BOOL=ON`: Always ignored  
- CMake 3.12: All runs OFF  
- CMake 3.13: All runs OFF  
  
With `-DFOO=OFF` or `-DFOO:BOOL=OFF` it is always OFF (expected)  
  
When setting the non-cache variable to ON:  
```  
cmake_minimum_required(VERSION 3.5...3.16)  
project(Boost VERSION 1.81.0 LANGUAGES CXX)  
  
set(FOO ON)  
   
option(FOO "DD" ON)  
message(FATAL_ERROR "FOO ${FOO}")  
```  
  
With no option or `-DFOO=ON` or `-DFOO:BOOL=ON` it is ON  
  
`-DFOO=OFF`: Ignored on CMake 3.13+  
- CMake 3.12: All runs OFF  
- CMake 3.13: All runs ON  
  
`-DFOO:BOOL=OFF`: Always ignored  
- CMake 3.12: All runs ON  
- CMake 3.13: All runs ON  
  
So it only affects Boost-as-a-subdirectory use cases and only for CMake 3.12 and below but (I think) it will be ignoring the user-projects policy setting so if someone was setting `CMAKE_VISIBILITY_INLINES_HIDDEN` as a normal variable with a policy version of 3.12 (e.g. `cmake_minimum_required(VERSION 3.8)`) he might get a surprise.  
  
The worst case is the different behavior between subsequent runs without changes. Maybe we should wrap all our `option` usages into a function the uses the current value as the default when the variable is defined to make it consistent.
