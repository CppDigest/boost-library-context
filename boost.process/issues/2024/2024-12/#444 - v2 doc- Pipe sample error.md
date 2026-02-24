# #444 - v2 doc: Pipe sample error [Closed]

> Username: liuaifu  
> Created at: 2024-12-23 12:08:22 UTC  
> Updated at: 2024-12-27 12:30:15 UTC  
> Closed at: 2024-12-27 12:30:15 UTC  
> Url: https://github.com/boostorg/process/issues/444  

url: https://www.boost.org/doc/libs/1_87_0/doc/html/boost_process/v2.html#boost_process.v2.stdio  
  
    asio::io_context ctx;  
    asio::readable_pipe rp{ctx};  
      
    process proc(ctx, "/usr/bin/g++", {"--version"}, process_stdio{{ /* in to default */}, rp, { /* err to default */ }});  
    std::string output;  
      
    system::error_code ec;  
    rp.read(asio::dynamic_buffer(output), ec);   // <--- compile error: asio::readable_pipe not have read  
    assert(ec == asio::eof);  
    proc.wait();  
    
The error line:  
  
    rp.read(asio::dynamic_buffer(output), ec);  
  
should be:  
  
    asio::read(rp, asio::dynamic_buffer(output), ec);

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-12-25 05:27:58 UTC  
> Url: https://github.com/boostorg/process/issues/444#issuecomment-2561624305  

Thank you. Fixed in develop already.
