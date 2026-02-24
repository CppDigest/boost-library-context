# #45 - Warning declaration of 'env' hides global declaration [Closed]

> Username: petke  
> Created at: 2018-05-23 20:22:17 UTC  
> Updated at: 2018-06-18 01:52:19 UTC  
> Closed at: 2018-06-18 01:52:19 UTC  
> Url: https://github.com/boostorg/process/issues/45  

The following hpp files both use the same name "env" which can cause comiple warning/error.  
```  
  
//\boost\process\environment.hpp  
explicit entry(string_type &&name, environment_t & env)   
  
//\boost\process\env.hpp  
constexpr boost::process::detail::env_ env{};  
```  
  
This probably has to do with the order I happen to include headers and compile my source files, after a refractoring of my program. I remember getting the same error about a year ago. Instead of trying to figure out a working ordering, it was easier this time to simply rename all the conflicting argument names in environment.hpp from "env" to "env_". If the argument name doesn't matter, you might want to do something similar, in case this happens to other people also.  
  
details:  
```  
\boost_1_67_0\boost\process\environment.hpp(97): error C2220: warning treated as error - no 'object' file generated  
\boost_1_67_0\boost\process\environment.hpp(97): warning C4459: declaration of 'env' hides global declaration  
\boost_1_67_0\boost\process\env.hpp(497): note: see declaration of 'boost::process::env'  
```
