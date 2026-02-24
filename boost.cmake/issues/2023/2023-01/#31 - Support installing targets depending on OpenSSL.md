# #31 - Support installing targets depending on OpenSSL [Closed]

> Username: anarthal  
> Created at: 2023-01-02 12:30:39 UTC  
> Updated at: 2023-01-02 15:37:02 UTC  
> Closed at: 2023-01-02 15:37:02 UTC  
> Url: https://github.com/boostorg/cmake/issues/31  

When installing a target that links to OpenSSL, importing it via `find_package` fails because the resulting Boost config file doesn't include a call to `find_dependency(OpenSSL)`.
