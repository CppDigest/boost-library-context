# #285 - Boost Asio doesn't provide move semantic for handlers [Closed]

> Username: ecoindev  
> Created at: 2019-09-12 15:20:12 UTC  
> Updated at: 2019-09-29 20:19:50 UTC  
> Closed at: 2019-09-29 20:19:50 UTC  
> Url: https://github.com/boostorg/asio/issues/285  

Boost Asio doesn't provide move semantic for handlers:  
  
```  
    boost::asio::io_context io;  
    auto uniq = std::make_unique<std::string>("Blah blah blah");  
    io.post([uniq = std::move(uniq)](){});  
```  
  
error: call to implicitly-deleted copy constructor  
  
Example: https://wandbox.org/permlink/oLbaRj7MBGWDSJeU

---

## Comment 1

> Username: djarek  
> Created at: 2019-09-12 18:13:03 UTC  
> Url: https://github.com/boostorg/asio/issues/285#issuecomment-530942605  

`boost::asio::io_context::post` is part of the legacy interface (which requires copy constructible), use the 1.66+ API instead:  
  
`asio::post(io, [uniq = std::move(uniq)](){})`

---

## Comment 2

> Username: ecoindev  
> Created at: 2019-09-13 07:47:05 UTC  
> Url: https://github.com/boostorg/asio/issues/285#issuecomment-531136553  

Oh my God! I need to check my code. I still use this legacy code everywhere :-)  
  
Thanks!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-09-13 21:35:10 UTC  
> Url: https://github.com/boostorg/asio/issues/285#issuecomment-531398581  

Define `BOOST_ASIO_NO_DEPRECATED` in your project to get help from the compiler

---

## Comment 4

> Username: ecoindev  
> Created at: 2019-09-17 19:38:11 UTC  
> Url: https://github.com/boostorg/asio/issues/285#issuecomment-532370207  

Thanks a lot!
