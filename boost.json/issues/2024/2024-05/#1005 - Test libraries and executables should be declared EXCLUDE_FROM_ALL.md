# #1005 - Test libraries and executables should be declared EXCLUDE_FROM_ALL [Closed]

> Username: pdimov  
> Created at: 2024-05-14 18:09:26 UTC  
> Updated at: 2024-05-17 15:40:59 UTC  
> Closed at: 2024-05-17 15:40:59 UTC  
> Url: https://github.com/boostorg/json/issues/1005  

The Boost convention is that `cmake --build .` only builds the libraries. Test libraries and executables are built with `cmake --build . --target tests`.
