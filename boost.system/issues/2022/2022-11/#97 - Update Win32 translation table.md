# #97 - Update Win32 translation table [Closed]

> Username: pdimov  
> Created at: 2022-11-08 23:18:10 UTC  
> Updated at: 2022-12-10 19:52:00 UTC  
> Closed at: 2022-12-10 19:52:00 UTC  
> Url: https://github.com/boostorg/system/issues/97  

... to match  
  
https://github.com/microsoft/STL/pull/2744  
  
and  
  
https://github.com/microsoft/STL/pull/2899

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-13 16:13:36 UTC  
> Url: https://github.com/boostorg/system/issues/97#issuecomment-1312766921  

Maybe add a test, too.  
```  
#include <boost/system.hpp>  
#include <system_error>  
#include <iostream>  
  
namespace sys = boost::system;  
  
int main()  
{  
    int n = 0;  
  
    for( int i = 0; i < 65000; ++i )  
    {  
        sys::error_code ec1( i, sys::system_category() );  
        sys::error_condition en1 = ec1.default_error_condition();  
  
        std::error_code ec2( i, std::system_category() );  
        std::error_condition en2 = ec2.default_error_condition();  
  
        if( en1 != en2 )  
        {  
            std::cout << en1 << " (" << en1.message() << ") != cond:" << en2.category().name() << ":" << en2.value() << " (" << en2.message() << ")\n";  
  
            if( en2.category() == std::generic_category() )  
            {  
                ++n;  
            }  
        }  
    }  
  
    return n < 256 ? n: 255;  
}  
```
