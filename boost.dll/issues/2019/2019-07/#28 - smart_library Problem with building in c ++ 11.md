# #28 - smart_library Problem with building in c ++ 11 [Closed]

> Username: choiwooseok  
> Created at: 2019-07-15 08:27:47 UTC  
> Updated at: 2020-01-29 21:07:01 UTC  
> Closed at: 2020-01-29 21:07:01 UTC  
> Url: https://github.com/boostorg/dll/issues/28  

```  
clang version 7.0.0-3~ubuntu0.18.04.1 (tags/RELEASE_700/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
```  
/include/boost/dll/smart_library.hpp:446:1: error: 'auto' return without trailing return type; deduced return types are a C++14 extension  
auto get(const smart_library& sm, const std::string &name, typename boost::enable_if<boost::is_function<T>>::type* = nullptr)  
```  
  
* Hi, I would like to build with c ++ 11. Can you guys fix this problem?? I think decltype would be enough.  
  
```  
template<class T>  
auto get(const smart_library& sm, const std::string &name, typename boost::enable_if<boost::is_function<T>>::type* = nullptr) -> decltype(sm.get_function<T>(name))  
{  
    return sm.get_function<T>(name);  
}  
```
