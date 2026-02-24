# #98 - using type declaration output as member function [Open]

> Username: vinniefalco  
> Created at: 2021-10-08 03:25:24 UTC  
> Updated at: 2021-10-08 03:25:24 UTC  
> Url: https://github.com/boostorg/docca/issues/98  

A nested "using" statement which declares a type is instead listed as a member function.  
Example:  
```  
class params  
{  
public:  
    /** A reference to an element.  
    */  
    /**@{*/  
    struct reference  
    {  
    };  
    using const_reference = reference;  
    /**@}*/  
};  
```  
  
Note how `const_reference` is in the Member Functions:  
  
![image](https://user-images.githubusercontent.com/1503976/136493415-74138e42-217c-45f7-ac80-d6d92eba5422.png)
