# #55 - Add BOOST_PP_BOOL_true and BOOST_PP_BOOL_false definitions [Open]

> Username: white-echidna  
> Created at: 2023-12-16 12:44:01 UTC  
> Updated at: 2023-12-16 12:44:01 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/55  

I suggest to define a new macro  
```c++  
#define BOOST_PP_BOOL_true 1  
#define BOOST_PP_BOOL_false 0  
```  
for make that possible:  
```c++  
BOOST_PP_IF(BOOST_PP_BOOL(true/false), "YES", "NO")  
//          ^-- Expands into 1/0  
```  
It seems like simple behavior and I have no idea why its not added yet.
