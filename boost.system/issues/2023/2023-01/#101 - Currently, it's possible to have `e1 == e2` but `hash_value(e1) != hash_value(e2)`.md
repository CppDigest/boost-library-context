# #101 - Currently, it's possible to have `e1 == e2` but `hash_value(e1) != hash_value(e2)` [Closed]

> Username: pdimov  
> Created at: 2023-01-07 03:56:42 UTC  
> Updated at: 2023-01-07 23:58:38 UTC  
> Closed at: 2023-01-07 23:58:38 UTC  
> Url: https://github.com/boostorg/system/issues/101  

```  
#include <boost/system.hpp>  
#include <system_error>  
#include <cerrno>  
#include <iostream>  
  
int main()  
{  
    std::error_code e1( EINVAL, std::generic_category() );  
  
    boost::system::error_code e2( e1 );  
    boost::system::error_code e3( e2.value(), e2.category() );  
  
    std::cout << "e2 == e3: " << std::boolalpha << ( e2 == e3 )  
        << "; hash_value(e2) == " << hash_value( e2 )  
        << "; hash_value(e3) == " << hash_value( e3 )  
        << std::endl;  
}  
```  
A code wrapping a `std::error_code` should probably never compare equal to a code not wrapping one.
