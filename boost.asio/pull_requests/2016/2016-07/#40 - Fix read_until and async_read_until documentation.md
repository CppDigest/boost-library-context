# #40 Fix read_until and async_read_until documentation [Closed]

> Username: patlkli  
> Created at: 2016-07-18 11:23:56 UTC  
> Updated at: 2017-12-02 07:41:53 UTC  
> Closed at: 2017-12-02 07:41:53 UTC  
> Url: https://github.com/boostorg/asio/pull/40  

Changed passage pretending, that std::getline would copy the delimiter into the output string, which it doesn't.  
  
See 1.2b here: http://en.cppreference.com/w/cpp/string/basic_string/getline

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:41:53 UTC  
> Url: https://github.com/boostorg/asio/pull/40#issuecomment-348675216  

Fixed in develop.

---
