# #164 - Improve error reporting for `symbols` [Closed]

> Username: AlexBAV  
> Created at: 2024-03-19 17:18:09 UTC  
> Updated at: 2024-03-23 21:02:31 UTC  
> Closed at: 2024-03-23 21:02:31 UTC  
> Url: https://github.com/boostorg/parser/issues/164  

Currently, if `symbols` parser is used as an expectation point and no value matches, something similar to the following is generated as an error message:  
  
```cpp  
symbols<EnumName> v { ... };  
  
parse(r, "command1=" > v);  
```  
  
Generated error:  
  
```  
1:32: error: Expected symbols<enum EnumName>[<<action>>] here  
```  
  
Is it possible to provide a way to set a custom "error message", maybe by introducing a new `symbols` constructor overload (and/or a member function that can be called to set such a message)?

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-20 08:21:24 UTC  
> Url: https://github.com/boostorg/parser/issues/164#issuecomment-2009011025  

This is clearly an oversight no my part.  Thanks for pointing this out!

---

## Comment 2

> Username: AlexBAV  
> Created at: 2024-03-20 08:32:02 UTC  
> Url: https://github.com/boostorg/parser/issues/164#issuecomment-2009026563  

That are really great news, thank you!
