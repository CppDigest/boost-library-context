# #2984 - All CMake targets must be prefixed with `boost_beast_` [Closed]

> Username: ashtum  
> Created at: 2025-02-13 09:48:45 UTC  
> Updated at: 2025-02-13 16:39:46 UTC  
> Closed at: 2025-02-13 16:39:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2984  

Since CMake test targets also appear in the Boost super-project (if tests are selected to be built), we must prefix them with `boost_beast_`.
