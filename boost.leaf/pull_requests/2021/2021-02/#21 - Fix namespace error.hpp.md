# #21 Fix namespace error.hpp [Closed]

> Username: museghost  
> Created at: 2021-02-23 14:48:53 UTC  
> Updated at: 2021-03-26 05:14:18 UTC  
> Closed at: 2021-03-26 05:14:17 UTC  
> Url: https://github.com/boostorg/leaf/pull/21  

To use BOOST_LEAF_NEW_ERROR macro, it should be declared as below in user's source or header file.  
Without them, the macro is not working.  
  
```c++  
//  leaf issue, BOOST_LEAF_NEW_ERROR macro, ::leaf::  
namespace leaf = boost::leaf;  
```  
  
For using LEAF without it, "::boost::" would be added in below namespace.  
  
As-is:  
```c++  
#define BOOST_LEAF_NEW_ERROR ::leaf::leaf_detail::inject_loc{__FILE__,__LINE__,__FUNCTION__}+::boost::leaf::new_error  
```  
  
To-be:  
```c++  
#define BOOST_LEAF_NEW_ERROR ::boost::leaf::leaf_detail::inject_loc{__FILE__,__LINE__,__FUNCTION__}+::boost::leaf::new_error  
```

---

## Comment 1

> Username: zajo  
> Created_at: 2021-03-26 05:14:17 UTC  
> Url: https://github.com/boostorg/leaf/pull/21#issuecomment-807941090  

I fixed this already in `develop`, thank you!

---
