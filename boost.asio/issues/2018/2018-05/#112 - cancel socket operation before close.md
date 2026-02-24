# #112 - cancel socket operation before close [Closed]

> Username: snahmad  
> Created at: 2018-05-11 10:56:08 UTC  
> Updated at: 2020-12-30 00:51:25 UTC  
> Closed at: 2020-12-30 00:51:24 UTC  
> Url: https://github.com/boostorg/asio/issues/112  

https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/basic_stream_socket/cancel/overload2.html  
  
without cancel. when I close socket, sometime I gets exception:  
  
Can I use it . I gets warning in visual studio because my project settings supports Windows XP.  
  
Also I gets memory leaks dump in visual studio 2015

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:51:23 UTC  
> Url: https://github.com/boostorg/asio/issues/112#issuecomment-752289531  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#617](https://github.com/chriskohlhoff/asio/issues/617).
