# #82 - BOOST_OVERRIDE is unknown override specifier [Closed]

> Username: mloskot  
> Created at: 2021-05-06 12:02:07 UTC  
> Updated at: 2021-05-06 15:39:34 UTC  
> Closed at: 2021-05-06 15:39:34 UTC  
> Url: https://github.com/boostorg/property_tree/issues/82  

/cc @madmongo1   
  
### Minimal Working Example  
  
https://godbolt.org/z/x4zx388h6  
  
```  
#include <boost/property_tree/detail/rapidxml.hpp>  
int main() {}  
```  
  
Use latest Boost 1.76 and compile with any compiler of your choice.  
I can not reproduce the problem with Boost 1.75.  
  
  
### Compilation Error  
  
- msvc  
  
```  
error C3646: 'BOOST_OVERRIDE': unknown override specifier  
```  
  
- clang  
```  
/opt/compiler-explorer/libs/boost_1_76_0/boost/property_tree/detail/rapidxml.hpp:61:41: error: expected ';' at end of declaration list  
        const char *what() const throw() BOOST_OVERRIDE  
                                        ^  
```  
  
- gcc  
```  
/opt/compiler-explorer/libs/boost_1_76_0/boost/property_tree/detail/rapidxml.hpp:61:40: error: expected ';' at end of member declaration  
   61 |         const char *what() const throw() BOOST_OVERRIDE  
      |                                        ^  
      |                                         ;  
/opt/compiler-explorer/libs/boost_1_76_0/boost/property_tree/detail/rapidxml.hpp:61:42: error: 'BOOST_OVERRIDE' does not name a type; did you mean 'BOOST_VERIFY'?  
   61 |         const char *what() const throw() BOOST_OVERRIDE  
      |                                          ^~~~~~~~~~~~~~  
      |                                          BOOST_VERIFY  
```  
  
### References  
  
It looks like PR #53 from @EugeneZelenko introduced this bug - likely, missing `#include <boost/config.hpp>`.  
There seem to be no related test case to check headers are self-contained.
