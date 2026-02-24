# #186 - Logger to binary file [Closed]

> Username: andreabonacini  
> Created at: 2022-06-20 10:20:20 UTC  
> Updated at: 2022-06-20 12:38:01 UTC  
> Closed at: 2022-06-20 12:38:00 UTC  
> Url: https://github.com/boostorg/log/issues/186  

Hi,  
  
I would like to perform a binary logging in my application for a specific file sink, while keeping another sink on a text mode logging.  
After calling:   
auto fileSink = add_file_log(..)  
i tried both fileSink->locked_backend()->set_open_mode(std::ios_base::binary) and keywords::open_mode = std::ios_base::binary without success.  
  
What am i missing?  
  
I'm using Boost Log version 1.72.0  
  
Thanks in advance

---

## Comment 1

> Username: Lastique  
> Created at: 2022-06-20 12:38:00 UTC  
> Url: https://github.com/boostorg/log/issues/186#issuecomment-1160397201  

`add_file_log` adds a sink [`text_file_backend`](https://www.boost.org/doc/libs/1_79_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.text_file), which is a text sink.  
  
Boost.Log does not provide binary logging sinks out of the box because there is no standardized binary format for logs. You can implement [your own sink backend](https://www.boost.org/doc/libs/1_79_0/libs/log/doc/html/log/extension.html#log.extension.sinks) that will write binary files in the format you need.
