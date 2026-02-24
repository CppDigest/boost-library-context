# #92 - scope '::' incorrectly removed from formatted code [Open]

> Username: vinniefalco  
> Created at: 2021-09-22 03:44:33 UTC  
> Updated at: 2021-09-22 03:44:55 UTC  
> Url: https://github.com/boostorg/docca/issues/92  

This javadoc:  
```  
/** Insert an element  
  
    This function participates in overload  
    resolution only if  
    `is_stringlike<T>::value == true`.  
*/  
```  
  
Produces:  
... `is_stringlike<T>value == true`  
  
Note how the two colons are missing  
  
Example:  
![image](https://user-images.githubusercontent.com/1503976/134280247-fe5b09c0-0e51-4da3-b4a4-30a4dc9c6696.png)
