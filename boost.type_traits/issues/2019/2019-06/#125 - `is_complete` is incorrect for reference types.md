# #125 - `is_complete` is incorrect for reference types [Open]

> Username: mattcalabrese  
> Created at: 2019-06-13 20:21:53 UTC  
> Updated at: 2019-06-13 20:21:53 UTC  
> Url: https://github.com/boostorg/type_traits/issues/125  

`is_complete<T&>::value` is currently defined to be the value of `is_complete<T>::value`. This is incorrect. According to the standard, all lvalue reference types and rvalue reference types are complete types.  
  
In _[basic.types]_:  
  
> A class that has been declared but not defined, an enumeration type in certain contexts (9.6), or an array of unknown bound or of incomplete element type, is an incompletely-defined object type. Incompletely-defined object types and cv void are incomplete types (6.7.1). Objects shall not be defined to have an incomplete type.  
  
References do not fall under any of the above cases and are therefore complete.
