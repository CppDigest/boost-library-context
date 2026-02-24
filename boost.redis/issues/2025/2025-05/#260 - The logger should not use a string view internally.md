# #260 - The logger should not use a string view internally [Closed]

> Username: mzimbres  
> Created at: 2025-05-29 12:47:41 UTC  
> Updated at: 2025-06-01 11:40:38 UTC  
> Closed at: 2025-06-01 11:40:38 UTC  
> Url: https://github.com/boostorg/redis/issues/260  

Keeping a `string_view` internally in the logger makes it extremely prone to memory bugs. It should be replaced by a string. See [here](https://github.com/boostorg/redis/blob/b58e4f94de7e495a3195ff8a986e790f42060448/include/boost/redis/logger.hpp#L135).

---

## Comment 1

> Username: anarthal  
> Created at: 2025-05-29 13:04:41 UTC  
> Url: https://github.com/boostorg/redis/issues/260#issuecomment-2919332090  

The shame with making it a `std::string` is that `logger` is no longer trivially copyable - while the design of the rest of the code seems to imply it is, as it copies the logger several times. We could store the prefix in the connection and make the logger point to it. Or simply refactor the rest of the code to take loggers by reference - which should also simplify type erasing the logger.
