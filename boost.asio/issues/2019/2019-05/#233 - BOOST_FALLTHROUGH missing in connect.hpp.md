# #233 - BOOST_FALLTHROUGH missing in connect.hpp [Closed]

> Username: JVApen  
> Created at: 2019-05-05 11:35:01 UTC  
> Updated at: 2020-12-30 01:03:55 UTC  
> Closed at: 2020-12-30 01:03:54 UTC  
> Url: https://github.com/boostorg/asio/issues/233  

I wasn't able to reproduce this on compiler explorer, most likely as this is triggered as combination clang-cl with Windows.  
  
boost/asio/impl/connect.hpp(524,11):  error: unannotated fall-through between switch labels [-Werror,-Wimplicit-fallthrough]  
  
Looking at the code in https://github.com/boostorg/asio/blob/develop/include/boost/asio/impl/connect.hpp we can confirm that not all branches of the switch statement `case 1:` result in a break.  
Would it be possible to add BOOST_FALLTHROUGH before the `default: ` label?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:03:53 UTC  
> Url: https://github.com/boostorg/asio/issues/233#issuecomment-752292025  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#689](https://github.com/chriskohlhoff/asio/issues/689).
