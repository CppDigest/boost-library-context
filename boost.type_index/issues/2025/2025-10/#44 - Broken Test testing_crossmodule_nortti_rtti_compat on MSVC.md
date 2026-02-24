# #44 - Broken Test testing_crossmodule_nortti_rtti_compat on MSVC [Closed]

> Username: Flamefire  
> Created at: 2025-10-13 12:58:04 UTC  
> Updated at: 2025-10-14 10:57:01 UTC  
> Closed at: 2025-10-14 10:01:24 UTC  
> Url: https://github.com/boostorg/type_index/issues/44  

The test fails to link due to missing `accept_typeindex`, see https://github.com/boostorg/type_index/actions/runs/18399048739/job/52423993160?pr=43  
  
The test looks like this:  
  
```  
add_library(boost_type_index_test_lib_rtti_compat SHARED test_lib.cpp)  
target_compile_options(boost_type_index_test_lib_rtti_compat PRIVATE ${BOOST_TYPEINDEX_DETAIL_RTTI})  
  
boost_test(TYPE run SOURCES testing_crossmodule.cpp LINK_LIBRARIES boost_type_index_test_lib_rtti_compat COMPILE_OPTIONS  ${BOOST_TYPEINDEX_DETAIL_NO_RTTI} NAME testing_crossmodule_nortti_rtti_compat)  
  
```  
So it compiles the test exe with "NO RTTI" and the library that defines it "WITH RTTI"  
  
This causes `_CPPRTTI` being defined in the library that is supposed to define it and the test doesn't has it defined causing it to try and invoke it.  
> // /GR- undefies the _CPPRTTI macro.   
  
This can never work, can it?  
  
  
In B2 it looks like this:  
  
```  
obj test_lib_rtti_compat-obj : test_lib.cpp : <link>shared $(nortti) $(compat) ;  
lib test_lib_rtti_compat : test_lib_rtti_compat-obj : <link>shared $(nortti) $(compat) ;  
  
    [ run testing_crossmodule.cpp test_lib_rtti_compat : : : $(nortti) $(compat) : testing_crossmodule_nortti_rtti_compat ]  
```  
  
Note that contrary to CMake "nortti" is used in both cases despite the name.

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-10-14 10:01:24 UTC  
> Url: https://github.com/boostorg/type_index/issues/44#issuecomment-3401049386  

Mostly fixed in https://github.com/boostorg/type_index/commit/a33e418f3992912838e3b32a0a82595b9f4ce88f  
Will continue investigations in separate PRs

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-10-14 10:57:01 UTC  
> Url: https://github.com/boostorg/type_index/issues/44#issuecomment-3401234276  

> Mostly fixed in [a33e418](https://github.com/boostorg/type_index/commit/a33e418f3992912838e3b32a0a82595b9f4ce88f)  
  
Note that that PR/commit is unrelated to this issue which seems to be a logic error / C&P issue in the test setup (both in Jamfiles and CMakeLists)
