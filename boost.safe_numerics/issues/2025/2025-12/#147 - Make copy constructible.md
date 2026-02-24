# #147 - [With example] Make copy constructible [Open]

> Username: Spixmaster  
> Created at: 2025-12-09 09:46:15 UTC  
> Updated at: 2025-12-09 09:46:15 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/147  

Source:  
```cpp  
std::unordered_map<boost::safe_numerics::safe<std::int32_t>, std::string> tmp;  
```  
  
Result:  
```sh  
/home/matheus/programmieren/c++/argo/src/database/User.cpp:508:87:   required from here  
  508 |             std::unordered_map<boost::safe_numerics::safe<std::int32_t>, std::string> tmp;  
      |                                                                                       ^~~  
/usr/include/c++/15.2.1/bits/hashtable.h:210:51: Fehler: statische Assertion fehlgeschlagen: hash function must be copy constructible  
  210 |       static_assert(is_copy_constructible<_Hash>::value,  
      |                                                   ^~~~~  
```
