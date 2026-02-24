# #185 - string_parser needs to initialize success flag [Closed]

> Username: cppljevans  
> Created at: 2024-08-11 08:07:21 UTC  
> Updated at: 2024-09-30 04:56:45 UTC  
> Closed at: 2024-09-30 04:56:44 UTC  
> Url: https://github.com/boostorg/parser/issues/185  

The string_parser::call needs to initialize the success out value to  
true.  
  
Here, in the string_parser::call function:  
  
  https://github.com/tzlaine/parser/blob/develop/include/boost/parser/parser.hpp#L6913  
    
the only change to the success flag is to false.  This is definitely  
wrong.  It needs to be initialized to true at the start of the  
function.  Otherwise, if the success flag is passed as false, it will  
not change its value, despite the parse being successful.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-09-30 04:56:44 UTC  
> Url: https://github.com/boostorg/parser/issues/185#issuecomment-2382070876  

Setting the status to `true` would be superfluous.  It is a precondition of each parser that `success == true`.  `success` is a `bool &`.  If we got into any parser and it were not `true`, that would mean that the parse failed, and yet the caller decided to start a new parse.  That doesn't happen. for obvious reasons.
