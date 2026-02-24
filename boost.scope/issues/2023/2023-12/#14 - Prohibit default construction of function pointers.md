# #14 - Prohibit default construction of function pointers [Closed]

> Username: Lastique  
> Created at: 2023-12-07 09:59:45 UTC  
> Updated at: 2023-12-17 16:18:18 UTC  
> Closed at: 2023-12-17 16:18:18 UTC  
> Url: https://github.com/boostorg/scope/issues/14  

Where default construction of function objects is allowed in the library, pointers to functions must be excluded because default- and value-constructed function pointers are not callable.
