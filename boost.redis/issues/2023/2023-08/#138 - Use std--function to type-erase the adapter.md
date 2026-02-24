# #138 - Use std::function to type-erase the adapter [Closed]

> Username: mzimbres  
> Created at: 2023-08-13 12:05:55 UTC  
> Updated at: 2023-08-26 13:39:50 UTC  
> Closed at: 2023-08-26 13:39:50 UTC  
> Url: https://github.com/boostorg/redis/issues/138  

Event multiplexing without type-erasing the adapter is making the code too complex and hits performance negatively on some places.  I think we can use `std::function` to avoid that. If the dynamic allocations become too costly we will need a class that offers better control over SBO.
