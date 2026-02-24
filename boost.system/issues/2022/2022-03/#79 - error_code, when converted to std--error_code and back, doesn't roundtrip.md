# #79 - error_code, when converted to std::error_code and back, doesn't roundtrip [Closed]

> Username: pdimov  
> Created at: 2022-03-17 18:51:51 UTC  
> Updated at: 2022-04-21 16:00:05 UTC  
> Closed at: 2022-04-21 16:00:05 UTC  
> Url: https://github.com/boostorg/system/issues/79  

As reported by @ned14 in https://wandbox.org/permlink/FateL4bRSowzQHDM.  
  
```  
#include <boost/asio.hpp>  
#include <system_error>  
  
int main(void)  
{  
    if(boost::system::error_code(std::error_code(boost::system::error_code(boost::asio::error::eof))) != boost::asio::error::eof)  
    {  
        abort();  
    }  
    return 0;  
}  
```
