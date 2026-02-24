# #530 - sun toolset needs to pass -std=c++11 to compiler [Closed]

> Username: vahtis  
> Created at: 2020-01-29 11:22:05 UTC  
> Updated at: 2020-01-29 15:11:26 UTC  
> Closed at: 2020-01-29 15:11:26 UTC  
> Url: https://github.com/boostorg/build/issues/530  

As sysinfo uses c++11 features build.sh needs to pass -std=c++11 to compiler. Otherwise build fails
