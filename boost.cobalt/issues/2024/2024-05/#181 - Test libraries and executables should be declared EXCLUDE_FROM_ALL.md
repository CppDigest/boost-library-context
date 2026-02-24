# #181 - Test libraries and executables should be declared EXCLUDE_FROM_ALL [Closed]

> Username: pdimov  
> Created at: 2024-05-14 18:07:18 UTC  
> Updated at: 2024-05-30 15:33:36 UTC  
> Closed at: 2024-05-30 15:33:36 UTC  
> Url: https://github.com/boostorg/cobalt/issues/181  

The Boost convention is that `cmake --build .` only builds the libraries. Test libraries and executables are built with `cmake --build . --target tests`.
