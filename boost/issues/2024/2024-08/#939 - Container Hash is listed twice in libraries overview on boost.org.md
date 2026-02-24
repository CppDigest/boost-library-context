# #939 - Container Hash is listed twice in libraries overview on boost.org [Open]

> Username: saimen  
> Created at: 2024-08-18 09:18:36 UTC  
> Updated at: 2024-08-18 09:18:36 UTC  
> Url: https://github.com/boostorg/boost/issues/939  

When checking the [list of boost libraries on boost.org](https://www.boost.org/doc/libs/),  
the "Container Hash" library is listed twice. The issue started with Boost 1.78. I tried to figure out what's causing this but can only state a wild guess: The [libraries.json](https://github.com/boostorg/container_hash/blob/develop/meta/libraries.json) contains information in the `std` and `cxxstd` keys. I have no clue if this leads to the double listing, but it definitely looks wrong.  
![boost_duplicate](https://github.com/user-attachments/assets/cc999e67-b00a-4cec-abdf-74c85194027d)
