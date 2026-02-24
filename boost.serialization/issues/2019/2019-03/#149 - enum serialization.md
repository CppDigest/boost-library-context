# #149 - enum serialization [Open]

> Username: sergegers  
> Created at: 2019-03-15 01:56:54 UTC  
> Updated at: 2019-03-15 01:56:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/149  

During the serialization enumerations are considering as a special case and serializing as int now. There are some drawbacks following this way  
- a shorter enums occupy 4 bytes anyway for binary serialization;  
-  input/output operators are ignored during XML serialization.  
  
It will be helpful to have an option to serialize enumerations as is. May be a new macro introduction (smth like BOOST_SERIALIZATION_KEEP_ENUM) could be a solution  
- compilation w/o macro in old way to support backward compatibility  
- recompilation with defined macro in a bright new way.  
  
Thanks
