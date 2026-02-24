# #412 - [v2][Windows] Cannot redirect stdout to a file [Open]

> Username: TonyMSantander  
> Created at: 2024-10-18 08:45:20 UTC  
> Updated at: 2025-11-17 08:09:41 UTC  
> Url: https://github.com/boostorg/process/issues/412  

I am using boost process v2 from the 1.86 version of the boost library.  
  
The following code is intended to redirect stdout to a file. It creates the file but the contents of stdout are not written to it; the file size is zero. When I redirect stdout to a pipe, the pipe contains the expected result.  
  
I guessed at how to write the code to redirect stdout to a file, as I could not find any example in the documentation or online. So I have probably not done it right.  
  
```  
#include <boost/process/v2.hpp>  
#include <boost/asio/read.hpp>  
#include <boost/asio/readable_pipe.hpp>  
#include <boost/asio/basic_stream_file.hpp>  
  
namespace proc   = boost::process::v2;  
namespace asio   = boost::asio;  
  
std::tuple<int, std::string> execProc(const std::string& exePath, const std::vector<std::string>& args,  
    const std::string& outputFilePath)  
{  
    asio::io_context ctx;  
  
    asio::basic_stream_file<asio::any_io_executor> outStream(ctx, outputFilePath.c_str(),   
        asio::file_base::write_only | asio::file_base::create | asio::file_base::exclusive);  
  
    asio::readable_pipe errPipe{ctx};  
  
    proc::process proc{ ctx, exePath, args, proc::process_stdio{{}, outStream, errPipe}};  
  
    std::string error;  
    boost::system::error_code ec;  
    auto errLen = asio::read(errPipe, asio::dynamic_buffer(error), ec);  
    auto exitCode = proc.wait();  
    outStream.close();  
  
    return std::make_tuple(exitCode, std::move(error));  
}  
  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-10-24 22:24:21 UTC  
> Updated at: 2024-10-24 23:49:13 UTC  
> Url: https://github.com/boostorg/process/issues/412#issuecomment-2436444738  

Can you try the same without the exclusive flag?

---

## Comment 2

> Username: NeroBurner  
> Created at: 2025-11-17 08:09:41 UTC  
> Url: https://github.com/boostorg/process/issues/412#issuecomment-3540457678  

could be related to https://github.com/boostorg/process/issues/448  
  
so maybe fixed?
