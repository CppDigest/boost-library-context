# #42 Fix -Wunused-parameter in quat_operations.hpp [Merged]

> Username: jhasse  
> Created at: 2023-01-18 10:19:33 UTC  
> Updated at: 2023-01-21 12:10:54 UTC  
> Merged at: 2023-01-21 06:17:36 UTC  
> Closed at: 2023-01-21 06:17:36 UTC  
> Url: https://github.com/boostorg/qvm/pull/42  

```  
/opt/homebrew/include/boost/qvm/quat_operations.hpp:237:43: warning: unused parameter 'x' [-Wunused-parameter]  
    read_element( this_quaternion const & x )  
                                          ^  
/opt/homebrew/include/boost/qvm/quat_operations.hpp:247:54: warning: unused parameter 'x' [-Wunused-parameter]  
    read_element_idx( int i, this_quaternion const & x )  
                                                     ^  
/opt/homebrew/include/boost/qvm/quat_operations.hpp:904:43: warning: unused parameter 'x' [-Wunused-parameter]  
    read_element( this_quaternion const & x )  
                                          ^  
/opt/homebrew/include/boost/qvm/quat_operations.hpp:914:54: warning: unused parameter 'x' [-Wunused-parameter]  
    read_element_idx( int i, this_quaternion const & x )  
```

---
