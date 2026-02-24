# #9 - Issue when including token_functions.hpp on its own. [Closed]

> Username: matovitch  
> Created at: 2018-10-22 13:48:58 UTC  
> Updated at: 2018-10-22 17:20:58 UTC  
> Closed at: 2018-10-22 17:20:58 UTC  
> Url: https://github.com/boostorg/tokenizer/issues/9  

Hello,  
  
Currently working with boost 1.59.0 I am not able to include token_functions.hpp on its own because of the use of is_pointer here: https://github.com/boostorg/tokenizer/blob/develop/include/boost/token_functions.hpp#L326.  
  
`include/boost/token_functions.hpp:327:31: error: 'is_pointer' was not declared in this scope`  
  
It seems more than 14 years ago someone stubled upon this very error: https://marc.info/?l=boost&m=118835577625533.  
  
I am not sure if this is still an issue with current boost version (maybe is it included from somewhere else, with the dependencies being what they are who knows ? ;)).  
  
The state of github being what it is today, I am unable to fork and/or submit any PR, but adding the header `#include <boost/type_traits/is_pointer.hpp>` should do the trick.  
  
Have a nice day !

---

## Comment 1

> Username: eldiener  
> Created at: 2018-10-22 17:20:58 UTC  
> Url: https://github.com/boostorg/tokenizer/issues/9#issuecomment-431904468  

I merged your PR.
