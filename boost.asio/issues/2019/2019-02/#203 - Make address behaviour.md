# #203 - Make address behaviour [Closed]

> Username: aimable-citron  
> Created at: 2019-02-18 13:29:06 UTC  
> Updated at: 2020-12-30 01:00:04 UTC  
> Closed at: 2020-12-30 01:00:03 UTC  
> Url: https://github.com/boostorg/asio/issues/203  

Hello,  
  
The behaviour from this code is different on windows and linux:  
  
    boost::asio::ip::address test = boost::asio::ip::make_address("1500");  
    std::cout << test.to_string() << std::endl;  
  
Is it normal?  
  
regards

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:00:02 UTC  
> Url: https://github.com/boostorg/asio/issues/203#issuecomment-752291174  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#674](https://github.com/chriskohlhoff/asio/issues/674).
