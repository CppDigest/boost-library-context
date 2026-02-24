# #200 - Remove unnecessary null pointer checks [Closed]

> Username: elfring  
> Created at: 2019-01-21 16:34:58 UTC  
> Updated at: 2020-12-30 00:59:46 UTC  
> Closed at: 2020-12-30 00:59:45 UTC  
> Url: https://github.com/boostorg/asio/issues/200  

[An extra null pointer check is not needed in functions](https://isocpp.org/wiki/faq/freestore-mgmt#delete-handles-null "Do I need to check for null before delete p?") like the following.  
* [thread_info_base](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/thread_info_base.hpp#L48 "Destructor for thread_info_base")  
* [winrt_ssocket_service_base::close](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/impl/winrt_ssocket_service_base.ipp#L138 "winrt_ssocket_service_base::close function")

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:59:44 UTC  
> Url: https://github.com/boostorg/asio/issues/200#issuecomment-752291133  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#671](https://github.com/chriskohlhoff/asio/issues/671).
