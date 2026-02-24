# #6 - Warning when building with clang 5 [Open]

> Username: krojew  
> Created at: 2018-07-23 08:46:39 UTC  
> Updated at: 2018-07-23 08:46:39 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/6  

When including scope_exit.hpp, the following warning (and thus error, if configured) is produced:  
  
```  
/boost/scope_exit.hpp:258:10: error: extension used [-Werror,-Wlanguage-extension-token]  
#include BOOST_TYPEOF_INCREMENT_REGISTRATION_GROUP()  
         ^  
/boost/typeof/typeof.hpp:197:13: note: expanded from macro 'BOOST_TYPEOF_INCREMENT_REGISTRATION_GROUP'  
     <boost/typeof/incr_registration_group.hpp>  
```
