# #223 - Redundent call to boost_install() [Closed]

> Username: jdumas  
> Created at: 2023-11-16 01:26:32 UTC  
> Updated at: 2023-11-16 01:31:49 UTC  
> Closed at: 2023-11-16 01:31:38 UTC  
> Url: https://github.com/boostorg/log/issues/223  

Hi.  
  
The call to  
https://github.com/boostorg/log/blob/ae9bf80017b881ca7b1575093ec98cf130619492/CMakeLists.txt#L657  
seems to be conflicting with the `__boost_auto_install()` from the `boostorg/cmake` project:  
https://github.com/boostorg/cmake/blob/ff2f83eaac0a04000bd4dcbdd2cba7ad1bba6f43/include/BoostRoot.cmake#L315-L317  
  
This result in `boost_install()` being called twice on the `boost_log` target, causing CMake to complain about `boost_log` being in multiple export sets:  
```  
 CMake Error: install(EXPORT "boost_log-targets" ...) includes target "boost_log" more than once in the export set.  
 ```  
   
 I think removing this line from boost::log's CMakeLists.txt would solve the problem. `Boost::serialization` has the same issue, but idk if I should report it as a separate issue.

---

## Comment 1

> Username: jdumas  
> Created at: 2023-11-16 01:31:39 UTC  
> Updated at: 2023-11-16 01:31:49 UTC  
> Url: https://github.com/boostorg/log/issues/223#issuecomment-1813597876  

Nvm I think I misreported the problem. I had commented [this check](https://github.com/boostorg/cmake/blob/ff2f83eaac0a04000bd4dcbdd2cba7ad1bba6f43/include/BoostRoot.cmake#L172) which seems to prevent the double inclusion of the install rule. Apologies for the noise :)
