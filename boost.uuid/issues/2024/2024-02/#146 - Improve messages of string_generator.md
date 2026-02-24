# #146 - Improve messages of string_generator [Closed]

> Username: ivantishchenko  
> Created at: 2024-02-08 15:20:11 UTC  
> Updated at: 2025-09-04 00:45:41 UTC  
> Closed at: 2025-09-04 00:45:41 UTC  
> Url: https://github.com/boostorg/uuid/issues/146  

It would be nice to extend the exception message for   
https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/string_generator.hpp  
  
For example:  
- "" => "invalid uuid string ''. uuid cannot be empty'  
- "123------123" => "invalid uuid string '123------123'. uuid cannot contain consecutive dashes'.  
- ...

---

## Comment 1

> Username: pdimov  
> Created at: 2024-05-09 00:59:46 UTC  
> Url: https://github.com/boostorg/uuid/issues/146#issuecomment-2101736777  

The exception messages have been improved a bit, although they still don't really match what you suggest. "" now says "Invalid UUID string at position 0: unexpected end of input", whereas "123------123" says "Invalid UUID string at position 3: hex digit expected".
