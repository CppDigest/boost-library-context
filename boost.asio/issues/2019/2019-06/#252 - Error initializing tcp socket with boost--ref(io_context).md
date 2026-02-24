# #252 - Error initializing tcp socket with boost::ref(io_context) [Closed]

> Username: kkumar45  
> Created at: 2019-06-07 07:02:44 UTC  
> Updated at: 2020-12-30 01:06:16 UTC  
> Closed at: 2020-12-30 01:06:15 UTC  
> Url: https://github.com/boostorg/asio/issues/252  

Initializing tcp::socket with boost::ref(io_context) results in compilation error.  It looks like a regression in boost::asio.   
  
It can be reproduced easily with below code.   
  
`#include <boost/asio.hpp>  
#include <boost/core/ref.hpp>  
  
int main()  
{  
    boost::asio::io_context io_context;  
    boost::asio::ip::tcp::socket socket(boost::ref(io_context));  
    return 0;  
}  
`  
  
Error details :  
boost/asio/detail/io_object_impl.hpp:101:40: error: ‘const class boost::reference_wrapper<boost::asio::io_context>’ has no member named ‘get_executor’; did you mean ‘get_pointer’?  
       implementation_executor_(context.get_executor(),  
                                ~~~~~~~~^~~~~~~~~~~~  
  
I have attached a patch, which works as a workaround for the issue. (file: boost/asio/detail/io_object_impl.hpp)  
  
![io_object_impl](https://user-images.githubusercontent.com/26009849/59086755-4de01280-8920-11e9-8c66-37411b7306df.JPG)

---

## Comment 1

> Username: djarek  
> Created at: 2019-06-07 12:45:26 UTC  
> Url: https://github.com/boostorg/asio/issues/252#issuecomment-499870837  

https://github.com/boostorg/asio/issues/227

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:06:14 UTC  
> Url: https://github.com/boostorg/asio/issues/252#issuecomment-752292426  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#701](https://github.com/chriskohlhoff/asio/issues/701).
