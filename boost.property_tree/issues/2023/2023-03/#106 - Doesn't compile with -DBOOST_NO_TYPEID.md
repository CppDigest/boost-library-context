# #106 - Doesn't compile with -DBOOST_NO_TYPEID [Closed]

> Username: erestor  
> Created at: 2023-03-31 13:27:29 UTC  
> Updated at: 2023-11-14 14:00:40 UTC  
> Closed at: 2023-11-14 14:00:39 UTC  
> Url: https://github.com/boostorg/property_tree/issues/106  

Only two lines in the whole code use typeid, just to show an error message. This can be circumvented by using boost::typeindex::type_id when compiling with -DBOOST_NO_TYPEID in order to succeed with the compilation.

---

## Comment 1

> Username: ashtum  
> Created at: 2023-11-14 14:00:22 UTC  
> Url: https://github.com/boostorg/property_tree/issues/106#issuecomment-1810257971  

Addressed in https://github.com/boostorg/property_tree/commit/3b7c3ccb6bf5438c1a2d1b663762d69760a6386d.
