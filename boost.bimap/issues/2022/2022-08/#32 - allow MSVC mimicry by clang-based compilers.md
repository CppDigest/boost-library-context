# #32 - allow MSVC mimicry by clang-based compilers [Closed]

> Username: ilatypov  
> Created at: 2022-08-11 02:25:04 UTC  
> Updated at: 2023-06-16 16:06:29 UTC  
> Closed at: 2022-09-02 20:45:31 UTC  
> Url: https://github.com/boostorg/bimap/issues/32  

MSVC-like compilers fail to process the older definitions `BOOST_BIMAP_MAP_VIEW_BASE_FRIEND` and `BOOST_BIMAP_SET_VIEW_BASE_FRIEND` but are forced into them by checks of the `BOOST_MSVC` auto-detected define.  
  
Depending on the compiler, Boost defines the above macros to expand into an appropriate form of a friend class declaration.  It has to sense the compiler because MSVC did not accept an instance of a template class as a friend "right away".    
```c++  
friend class tplclass<T>;  
```  
MSVC needed an additional typedef to make that work.  
```c++  
typedef tplclass<T> tplinstclass;  
friend class tplinstclass;  
```  
  
One clang-based static analyzer mimics MSVC in many aspects but does not agree with the Boost work-around.  It rejects the above Boost's work-around for MSVC because [usage of a `typedef`-ed class name is not taking the `class` specifier](https://stackoverflow.com/a/15501746/80772).  
```  
include\boost/bimap/views/map_view.hpp:57:5: error: typedef 'friend_map_view_base' cannot be referenced with a class specifier  
include\boost/bimap/views/set_view.hpp:56:5: error: typedef 'template_class_friend' cannot be referenced with a class specifier  
```  
  
I therefore suggest limiting the older friend class declaration to the older MSVC versions,  
```diff  
-#if defined(BOOST_MSVC)  
+#if defined(BOOST_MSVC) && (_MSC_VER < 1910)  
```  
Alternatively, the `typedef`-ed name of kind `tplinstclass` can be used without the `class` specifier, that is, as `friend tplinstclass` in the existing BOOST_MSVC branch of the macro definitions.  But this may break builds of Boost consumers with older MSVC compilers because those builds are unlikely to be tested with the change.

---

## Comment 1

> Username: ilatypov  
> Created at: 2022-09-02 20:45:01 UTC  
> Url: https://github.com/boostorg/bimap/issues/32#issuecomment-1235881859  

Never mind, an upcoming release of the analyzer will mimic MSVC fully in this regard.

---

## Comment 2

> Username: RIscRIpt  
> Created at: 2023-06-16 16:06:29 UTC  
> Url: https://github.com/boostorg/bimap/issues/32#issuecomment-1594920973  

Could this issue still be addressed? Because the current code for MSVC is ill-formed, and thus not accepted by clang-cl (see llvm/llvm-project#63217).
