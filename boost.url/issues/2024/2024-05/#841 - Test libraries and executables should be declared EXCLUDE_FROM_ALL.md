# #841 - Test libraries and executables should be declared EXCLUDE_FROM_ALL [Closed]

> Username: pdimov  
> Created at: 2024-05-14 18:12:33 UTC  
> Updated at: 2024-08-20 00:48:56 UTC  
> Closed at: 2024-08-20 00:48:56 UTC  
> Url: https://github.com/boostorg/url/issues/841  

The Boost convention is that `cmake --build .` only builds the libraries. Test libraries and executables are built with `cmake --build . --target tests`.
