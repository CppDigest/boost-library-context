# #326 - Example of boost/python/call_method.hpp does not compile [Open]

> Username: CedricSchmeits  
> Created at: 2020-10-20 13:08:59 UTC  
> Updated at: 2020-10-20 13:09:34 UTC  
> Url: https://github.com/boostorg/python/issues/326  

I compilled the my_module.cpp file with the following command:  
`  
gcc -o my_module.o my_module.cpp -c -Wall -fPIC -I/usr/include/python3.6  
`  
  
This resulted in the following error:  
```  
my_module.cpp: In member function ‘virtual const char* Base_callback::class_name() const’:  
my_module.cpp:31:44: error: ‘call_method’ was not declared in this scope  
    char const* class_name() const { return call_method<char const*>(m_self, "class_name"); }  
                                            ^~~~~~~~~~~  
my_module.cpp:31:56: error: expected primary-expression before ‘char’  
    char const* class_name() const { return call_method<char const*>(m_self, "class_name"); }  
                                                        ^~~~  
my_module.cpp:31:56: error: expected ‘;’ before ‘char’  
my_module.cpp:31:67: error: expected unqualified-id before ‘>’ token  
    char const* class_name() const { return call_method<char const*>(m_self, "class_name"); }  
                                                                   ^  
my_module.cpp:31:67: error: expected initializer before ‘>’ token  
my_module.cpp: In function ‘void init_module_tryout()’:  
my_module.cpp:40:5: error: ‘def’ was not declared in this scope  
     def("is_base", is_base);  
     ^~~  
makefile:16: recipe for target 'my_module.o' failed  
```  
  
The solution was to add the following 2 include statements:  
```  
#include <boost/python/call_method.hpp>  
#include <boost/python/def.hpp>  
```
