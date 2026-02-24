# #246 - GCC 13 tests not run [Closed]

> Username: Flamefire  
> Created at: 2024-12-28 15:57:00 UTC  
> Updated at: 2025-01-02 13:48:15 UTC  
> Closed at: 2025-01-02 13:48:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/246  

https://github.com/boostorg/charconv/actions/runs/12527617354/job/34941378012?pr=242 shows that no configuration with GCC 13 is being run as it seems to fail every configure check.   
  
This is likely caused by the cxxflags passed and not parsed correctly
