# #460 - Support for merging stdout and stderr to a single pipe in v2 [Closed]

> Username: rconde01  
> Created at: 2025-01-26 19:23:44 UTC  
> Updated at: 2025-01-27 23:24:16 UTC  
> Closed at: 2025-01-27 16:17:16 UTC  
> Url: https://github.com/boostorg/process/issues/460  

As far as I can tell this is not possible. Specifying the same pipe for stdout and stderr gives an exception. I think perhaps if shell was added you could redirect within the shell, but i'm not sure.

---

## Comment 1

> Username: rconde01  
> Created at: 2025-01-27 16:17:16 UTC  
> Url: https://github.com/boostorg/process/issues/460#issuecomment-2616200015  

With @klemens-morgenstern assistance I was able to get it working with:  
```  
#include <iostream>  
  
#include <boost/asio.hpp>  
#include <boost/process/v2.hpp>  
  
auto main(int argc, char** argv) -> int {  
   if (argc == 1) {  
      boost::asio::io_context    io_context;  
      boost::asio::writable_pipe merged_write_pipe(io_context);  
      boost::asio::readable_pipe merged_read_pipe(io_context);  
  
      boost::asio::connect_pipe(merged_read_pipe, merged_write_pipe);  
  
      boost::process::v2::process p(  
         io_context,  
         argv[0],  
         {"nazz"},  
         boost::process::v2::process_stdio{  
            nullptr, merged_write_pipe, merged_write_pipe});  
  
      char                      buffer[1024];  
      boost::system::error_code ec;  
  
      for (;;) {  
         boost::asio::deadline_timer timer(  
            io_context, boost::posix_time::milliseconds(10));  
  
         timer.async_wait([&](boost::system::error_code const& a_ec) {  
            if (!a_ec)  
               merged_write_pipe.cancel();  
         });  
  
         merged_read_pipe.async_read_some(  
            boost::asio::buffer(buffer),  
            [&](boost::system::error_code const& a_ec, std::size_t bytes) {  
               timer.cancel();  
               if (!a_ec && bytes != 0) {  
                  std::cout.write(buffer, bytes);  
               }  
            });  
  
         io_context.run();  
      }  
   }  
   else {  
      size_t i{};  
  
      for (;;) {  
         std::cout << "Output " << i << std::endl;  
         std::cerr << "Error " << i << std::endl;  
  
         ++i;  
      }  
   }  
}  
```

---

## Comment 2

> Username: rconde01  
> Created at: 2025-01-27 23:24:15 UTC  
> Url: https://github.com/boostorg/process/issues/460#issuecomment-2617118292  

FYI This, as written above, does not work on linux.
