# #458 - Removal of global placeholders causes multiple software to fail even when BOOST_BIND_GLOBAL_PLACEHOLDERS is set [Open]

> Username: LanThrusteR  
> Created at: 2021-01-08 14:23:56 UTC  
> Updated at: 2021-01-08 14:23:56 UTC  
> Url: https://github.com/boostorg/boost/issues/458  

Multiple software suffered from boost global placeholders removal causing "_1 was not declared in this scope" like compiler errors. The backward compatibility was supposed to be addressed by BOOST_BIND_GLOBAL_PLACEHOLDERS, it looked like you define BOOST_BIND_GLOBAL_PLACEHOLDERS and then magically bind.hpp header is pulled in everything boost uses returning the old logic back.   
  
But that macros is just a plug that does nothing but:  
  
#if !defined(BOOST_BIND_GLOBAL_PLACEHOLDERS)  
  
BOOST_PRAGMA_MESSAGE(  
  "The practice of declaring the Bind placeholders (_1, _2, ...) "  
  "in the global namespace is deprecated. Please use "  
  "<boost/bind/bind.hpp> + using namespace boost::placeholders, "  
  "or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior."  
)  
  
#endif  
  
 defining or not defining it just affects the message, but not the logic.   
  
When BOOST_BIND_GLOBAL_PLACEHOLDERS is defined the old logic of registering global placeholders should be back with everything using any boost header for at least 2-3 next years when all the developers patch their software.   
  
Pulling _1 in the global scope might be the bad practice but that practice was set by boost and used widely by many developers
