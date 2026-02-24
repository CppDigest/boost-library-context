# #265 - Disable OpenSSL auto initialization [Closed]

> Username: dmorilha-twilio  
> Created at: 2019-07-25 21:24:13 UTC  
> Updated at: 2020-12-30 01:07:47 UTC  
> Closed at: 2020-12-30 01:07:46 UTC  
> Url: https://github.com/boostorg/asio/issues/265  

We use boost asio together with other libraries which depend on OpenSSL. Currently OpenSSL is initialized multiple times. We would like to be able to disable openssl initialization in boost asio.  
  
During a small investigation, I was able to find this issue that somewhat describes the problem: https://github.com/chriskohlhoff/asio/issues/73  
  
Going further into the investigation, I found [openssl_init](https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/openssl_init.hpp#L55) class which has a boolean template parameter. Unfortunately this class is poorly documented and of nontrivial understanding.  
  
When trying to instantiate the templated class with `false` I saw no differences. As a matter of fact, `nm` shows both instances in the data section:  
  
```  
0000000100048480 D boost::asio::ssl::detail::openssl_init<false>::instance_  
0000000100048448 D boost::asio::ssl::detail::openssl_init<true>::instance_  
```  
  
It would be awesome to understand how this functionality works, preferably with an example.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:07:44 UTC  
> Url: https://github.com/boostorg/asio/issues/265#issuecomment-752292667  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#708](https://github.com/chriskohlhoff/asio/issues/708).
