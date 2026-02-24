# #121 - Can generator is shared by multiple threads without locks? [Closed]

> Username: redboltz  
> Created at: 2021-10-18 04:20:50 UTC  
> Updated at: 2021-10-18 14:42:29 UTC  
> Closed at: 2021-10-18 14:42:29 UTC  
> Url: https://github.com/boostorg/uuid/issues/121  

## Background  
  
I'm using Boost.Uuid 1.76.0  
  
I wrote the code as follows:  
The random_generator instance `gen` as static variable.  
  
```cpp  
#include <boost/uuid/uuid.hpp>  
#include <boost/uuid/uuid_generators.hpp>  
#include <boost/uuid/uuid_io.hpp>  
#include <iostream>  
  
inline std::string create_uuid_string() {  
    static auto gen = boost::uuids::random_generator();  
    return boost::uuids::to_string(gen());  
}  
  
int main() {  
    std::cout << create_uuid_string() << std::endl;  
    std::cout << create_uuid_string() << std::endl;  
}  
```  
  
https://wandbox.org/permlink/0sORjrX3QXtsO8zb  
  
## Question1  
  
According to the following document, I guess that calling `create_uuid_string()` from multiple threads without locks are not thread-safe. Because the function`create_uuid_string()` calls `gen()` , `operator()()` of `random_generator`  
  
https://github.com/boostorg/uuid/blob/eaa4be7b96c99ad56effc351aa44d0bef04da5a3/doc/uuid.html#L858-L859  
  
Is that right?  
  
## Question2  
  
If the Question1 is NOT thread-safe, I come up with two solution.  
One is introducing lock to access `gen()`. But it would introduce thread serialization point.  
It could be performance penalty.  
  
The other is removing `static` from `gen` declaration. That means creating `gen` for each `create_uuid_string()`.  
It doesn't require locks but each time creates `gen`.  
It could be performance penalty.  
  
```cpp  
inline std::string create_uuid_string() {  
    auto gen = boost::uuids::random_generator();  
    return boost::uuids::to_string(gen());  
}  
```  
  
Which approach is better performance? Or is there other better approach?

---

## Comment 1

> Username: Lastique  
> Created at: 2021-10-18 10:37:46 UTC  
> Url: https://github.com/boostorg/uuid/issues/121#issuecomment-945634961  

Whether the actual implementation `random_generator` is thread-safe depends on the implementation, but in general you should assume it is *not* thread safe and a single instance of `random_generator` cannot be accessed safely from multiple threads without external synchronization. That's what the docs say. So, your original code is not thread-safe, although it may appear to work fine on some targets.  
  
The solution is either use external synchronization or avoid sharing the `random_generator` instance between threads. One way to do the latter while also avoiding creating the instance on every call is to use thread-local storage.  
  
```  
inline std::string create_uuid_string() {  
    static thread_local boost::uuids::random_generator gen;  
    return boost::uuids::to_string(gen());  
}  
```

---

## Comment 2

> Username: redboltz  
> Created at: 2021-10-18 14:42:29 UTC  
> Url: https://github.com/boostorg/uuid/issues/121#issuecomment-945848601  

Thank you for the comment. thread_local approach is the best one for my usecase.  
generators creating cost is only one time per threads and then, no locks are required.  
Very nice!  
  
Thank you.
