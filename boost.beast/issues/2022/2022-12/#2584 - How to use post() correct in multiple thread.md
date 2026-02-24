# #2584 - How to use post() correct in multiple thread? [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:33:09 UTC  
> Updated at: 2022-12-13 15:52:57 UTC  
> Closed at: 2022-12-13 15:52:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2584  

### Discussed in https://github.com/boostorg/beast/discussions/2303  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **yanrk** August 27, 2021</sup>  
I have the code  
  
```  
    /* code 1 */  
    boost::asio::io_context ioc;  
  
    for (int i = 0; i < 5; ++i) {  
        std::thread([&]() {  
            ioc.run();  
        }).detach();  
    }  
  
    boost::beast::websocket::stream<boost::beast::tcp_stream> stream(boost::asio::make_strand(ioc));  
    boost::asio::io_context::strand strand(ioc); // for code 3  
    int i = 0;  
```  
  
Then, I want to do something correct with post, I want the post-callback in order  
  
So, I get code2:  
  
```  
    /* code 2, maybe call on multiple threads at same time */  
    boost::asio::post(stream.get_executor(), []() {  
        std::cout << "[" << std::this_thread::get_id() << "]" << ", i: " << ++i << std::endl;  
    });  
```  
and code3:  
  
```  
    /* code 3, maybe call on multiple threads at same time */  
    strand.post([]() {  
        std::cout << "[" << std::this_thread::get_id() << "]" << ", i: " << ++i << std::endl;  
    });  
```  
  
Which one can meet the needs of orderly callbacks? Thanks.  
</div>

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:52:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2584#issuecomment-1348870479  

2
