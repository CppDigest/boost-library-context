# #63 Improve to be able to use std::optional optionally [Closed]

> Username: ken-matsui  
> Created at: 2020-12-14 08:39:33 UTC  
> Updated at: 2023-03-09 19:50:47 UTC  
> Closed at: 2023-03-09 19:50:47 UTC  
> Url: https://github.com/boostorg/property_tree/pull/63  

I made std::optional be able to optionally use in property_tree by defining `BOOST_PROPERTY_TREE_USE_STD_OPTIONAL`.  
  
Usage in CMake:  
```cmake  
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_17)  
target_compile_definitions(${PROJECT_NAME} PRIVATE BOOST_PROPERTY_TREE_USE_STD_OPTIONAL)  
```

---
