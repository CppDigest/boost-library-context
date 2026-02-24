# #145 - basic_random_generator not safe in case a = a [Closed]

> Username: dasfex  
> Created at: 2023-12-05 07:47:19 UTC  
> Updated at: 2024-04-22 15:28:32 UTC  
> Closed at: 2024-04-22 15:28:31 UTC  
> Url: https://github.com/boostorg/uuid/issues/145  

https://github.com/boostorg/uuid/blob/2bc0c8e71677f387afdc09bc4f8d609d2c74e80e/include/boost/uuid/random_generator.hpp#L128  
  
Here we should check ```this == &that``` for cases when we try to self-assign object of that type. Wy we don't do it right now?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-22 15:28:31 UTC  
> Url: https://github.com/boostorg/uuid/issues/145#issuecomment-2069892556  

Should be fixed on develop.
