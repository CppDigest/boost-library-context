# #735 - Set Visual Studio solution filename [Closed]

> Username: vinniefalco  
> Created at: 2023-02-05 20:51:20 UTC  
> Updated at: 2025-09-04 23:21:14 UTC  
> Closed at: 2025-09-04 23:21:14 UTC  
> Url: https://github.com/boostorg/boost/issues/735  

If we could change CMakeLists.txt for the superproject then we can set the name of the solution otherwise it is always Boost.sln which is not ideal:  
```  
if( NOT DEFINED BOOST_PROJECT_NAME )  
  set( BOOST_PROJECT_NAME "Boost" )  
endif()  
project( ${BOOST_PROJECT_NAME} VERSION 1.82.0 LANGUAGES CXX )  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-16 23:34:46 UTC  
> Url: https://github.com/boostorg/boost/issues/735#issuecomment-1433883965  

The problem is that if you build more than one solution for different libraries, the Task Manager shows them all with the name Boost and you can't tell one from the other when switching

---

## Comment 2

> Username: pdimov  
> Created at: 2023-02-17 02:01:07 UTC  
> Url: https://github.com/boostorg/boost/issues/735#issuecomment-1433989910  

No, sorry. The project name is significant and changing it just to rename the .sln file is not the right fix. You should ask in [the CMake discourse](https://discourse.cmake.org/) whether there's a way to change the name of the generated .sln file without changing the CMake project name.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-02-17 02:04:56 UTC  
> Updated at: 2023-02-17 02:12:43 UTC  
> Url: https://github.com/boostorg/boost/issues/735#issuecomment-1433993248  

Yeah, I'll do that, but note that you are currently calling the resulting solution "Boost" no matter what is in it (for example, even if you told cmake to only build one library). Although I guess that's one way of making "Boost" smaller.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-02-17 02:07:41 UTC  
> Url: https://github.com/boostorg/boost/issues/735#issuecomment-1433995722  

https://discourse.cmake.org/t/canonical-way-to-set-the-visual-studio-solution-sln-filename/7482

---

## Comment 5

> Username: pdimov  
> Created at: 2023-02-17 02:11:37 UTC  
> Url: https://github.com/boostorg/boost/issues/735#issuecomment-1433998166  

"Boost" is the name of the superproject. The libraries have their own project names ("boost_lib").

---

## Comment 6

> Username: vinniefalco  
> Created at: 2023-02-17 02:13:34 UTC  
> Updated at: 2023-02-17 02:14:23 UTC  
> Url: https://github.com/boostorg/boost/issues/735#issuecomment-1433999374  

> "Boost" is the name of the superproject  
  
Yes well my change renames the superproject...not any individual library.

---

## Comment 7

> Username: nigels-com  
> Created at: 2025-09-04 06:02:56 UTC  
> Url: https://github.com/boostorg/boost/issues/735#issuecomment-3252046293  

@vinniefalco is this issue still relevant?  
Curious if this one can be closed, or not.
