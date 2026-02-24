# #244 - ASIO examples use "deprecated" `io_service` [Closed]

> Username: martinitus  
> Created at: 2020-05-06 14:53:08 UTC  
> Updated at: 2020-05-07 10:23:51 UTC  
> Closed at: 2020-05-07 10:23:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/244  

Hi Folks,  
I think `boost::asio::io_service` got replaced by `boost::asio::io_context`.   
At least in 1.72 `io_service` is only a typedef forwarding to `io_context`.  
  
Maybe the examples can be modified so people learn the new stuff :)  
  
Hth,   
Martin

---

## Comment 1

> Username: olk  
> Created at: 2020-05-06 16:14:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/244#issuecomment-624744440  

you are welcome to provide a patch/pull request
