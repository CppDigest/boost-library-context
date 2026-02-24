# #52 - `malloc_n` method inconsistency from `boost::simple_segregated_storage`? [Closed]

> Username: leimao  
> Created at: 2022-12-11 19:17:55 UTC  
> Updated at: 2023-01-15 12:23:46 UTC  
> Closed at: 2023-01-15 12:23:46 UTC  
> Url: https://github.com/boostorg/pool/issues/52  

Hello Boost Pool developers,  
  
I was making some attempts to use `boost::simple_segregated_storage` by modifying the [example](https://theboostcpplibraries.com/boost.pool#ex.pool_01).  
  
This example worked.  
  
```c++  
#include <boost/pool/simple_segregated_storage.hpp>  
#include <cstddef>  
#include <vector>  
#include <cassert>  
#include <iostream>  
#include <stdexcept>  
  
int main()  
{  
    boost::simple_segregated_storage<std::size_t> storage;  
    std::vector<char> v1(5120);  
    storage.add_ordered_block(&v1.front(), v1.size(), 256);  
    int* j = static_cast<int*>(storage.malloc_n(1, 1024));  
    if (j == nullptr)  
    {  
        throw std::runtime_error{"Contiguous memory trunks not found."};  
    }  
    storage.ordered_free_n(j, 1, 1024);  
}  
```  
  
But this example did not work.  
  
```c++  
#include <boost/pool/simple_segregated_storage.hpp>  
#include <cstddef>  
#include <vector>  
#include <cassert>  
#include <iostream>  
#include <stdexcept>  
  
int main()  
{  
    boost::simple_segregated_storage<std::size_t> storage;  
    std::vector<char> v1(5120);  
    storage.add_ordered_block(&v1.front(), v1.size(), 256);  
    int* j = static_cast<int*>(storage.malloc_n(2, 1024));  
    if (j == nullptr)  
    {  
        throw std::runtime_error{"Contiguous memory trunks not found."};  
    }  
    storage.ordered_free_n(j, 2, 1024);  
}  
```  
  
If my understanding of `boost::simple_segregated_storage` from [here](https://www.boost.org/doc/libs/1_80_0/libs/pool/doc/html/boost_pool/pool/pooling.html#boost_pool.pool.pooling.concepts) is correct, the `boost::simple_segregated_storage` design should either allow or deny both of the examples above (I personally favor denying both of the examples), but not just one worked and the other one did not.  
  
Can you provide some clarifications about the `malloc_n` method here? Thank you.

---

## Comment 1

> Username: mclow  
> Created at: 2022-12-12 20:42:38 UTC  
> Url: https://github.com/boostorg/pool/issues/52#issuecomment-1347279987  

Either I'm misunderstanding something here, or `malloc_n` is pretty badly broken.  Note that if you change the call to `storage.malloc_n(1, 10240)`, it happily returns you a non-null pointer, even though there's no way it has 10K of storage.

---

## Comment 2

> Username: leimao  
> Created at: 2022-12-12 22:07:30 UTC  
> Updated at: 2022-12-12 22:08:27 UTC  
> Url: https://github.com/boostorg/pool/issues/52#issuecomment-1347407901  

> Either I'm misunderstanding something here, or `malloc_n` is pretty badly broken. Note that if you change the call to `storage.malloc_n(1, 10240)`, it happily returns you a non-null pointer, even though there's no way it has 10K of storage.  
  
Thanks a lot @mclow. In fact, I have never tried what you did before. I will try to reproduce this later today. If the issue can be reproduced, I will agree that `malloc_n` is badly broken.   
  
In my opinion, the `malloc_n` should return a nullptr when the trunk size has never been explicitly used in the block created via `add_block` or `add_ordered_block`.

---

## Comment 3

> Username: leimao  
> Created at: 2022-12-12 22:36:45 UTC  
> Url: https://github.com/boostorg/pool/issues/52#issuecomment-1347438303  

In addition, if we allow equivalent function calls, such as `malloc_n(1, 1024)`, `malloc_n(2, 512)`, `malloc_n(4, 256)`, it will be very confusing.

---

## Comment 4

> Username: leimao  
> Created at: 2022-12-14 03:12:13 UTC  
> Url: https://github.com/boostorg/pool/issues/52#issuecomment-1350323240  

> Either I'm misunderstanding something here, or `malloc_n` is pretty badly broken. Note that if you change the call to `storage.malloc_n(1, 10240)`, it happily returns you a non-null pointer, even though there's no way it has 10K of storage.  
  
I confirmed that this is reproducible for the latest Boost V1.80.0.
