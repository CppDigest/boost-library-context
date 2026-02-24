# #541 - asio::write and asio::read hangs [Closed]

> Username: Suresh3d  
> Created at: 2026-01-08 11:03:25 UTC  
> Updated at: 2026-01-08 11:31:19 UTC  
> Closed at: 2026-01-08 11:31:19 UTC  
> Url: https://github.com/boostorg/process/issues/541  

Hi ,   
  
My requirement is launch cmd or powershell , and write some commands and read output . Below is the code I am using,   
```  
asio::io_context ctx;  
asio::writable_pipe wp{ ctx };  
asio::readable_pipe rp{ ctx };  
asio::readable_pipe erp{ ctx };  
  
process proc(  
    ctx,   
    "C:\\Windows\\System32\\cmd.exe",   
    {},   
    bp::process_stdio{ wp, rp, erp }  
);  
std::string output;  
boost::system::error_code ec;  
asio::write(wp, asio::buffer("echo Hello from CMD\n"));  
boost::asio::read(rp, asio::dynamic_buffer(output),boost::asio::transfer_all());`  
```  
It hangs at `boost::asio::read(rp, asio::dynamic_buffer(output),boost::asio::transfer_all());`. Once the output is read I need to write another command and read output . Can anyone please help me to handle this issue ?   
  
tried below code as well ,   
```  
 for (;;)  
 {  
     char buf[1024];  
     std::size_t n = rp.read_some(asio::buffer(buf), ec);  
  
     if (ec == asio::error::eof)  
         break;  
  
     if (ec)  
         throw boost::system::system_error(ec);  
  
     std::cout.write(buf, n);  
 }  
```  
once everything is read , again it comes to read_some() and hangs . Is it waiting for something to be written ? as nothing is there it just listens ? if that is the case how can I write next command ? Should I deal with multi threading , one thread to write and one thread to read ?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2026-01-08 11:31:16 UTC  
> Url: https://github.com/boostorg/process/issues/541#issuecomment-3723455759  

`asio::read` will read until EOF, which in this case means until the pipe is closed. Your loop will do the same.  
  
You either:   
1. parse the output and stop reading at the correct time (e.g. a newline)  
2. stop the `cmd.exe` by writing an EOF or closing the write pipe after you're done (I think that'll end cmd.exe)  
3. use the asynchronous APIs with asio. No multithreading needed.
