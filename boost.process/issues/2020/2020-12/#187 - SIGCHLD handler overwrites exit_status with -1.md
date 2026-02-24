# #187 - [Linux] SIGCHLD handler overwrites exit_status with -1 [Open]

> Username: dkl  
> Created at: 2020-12-03 11:01:30 UTC  
> Updated at: 2020-12-03 11:01:30 UTC  
> Url: https://github.com/boostorg/process/issues/187  

Hello,  
  
here's a program demonstrating an issue where an incorrect exit status is returned for an exited child process, even though the correct one was known. It's a mix of `boost::process::on_exit()` and `boost::process::is_running()`, such that is_running()'s waitpid() reaps the child process, while the SIGCHLD handler's waitpid() runs afterwards (because the completion handler is queued later in the io_service). So the second waitpid() fails, and the SIGCHLD stores -1, overwriting the previously correct exit status.  
  
I've got a fix suggestion for this: https://github.com/dkl/boost-process/commit/ff33b70c8fa6da071028196bdc277825c4e4f4d4  
but I'm still trying to figure out how to add a test case for that, if possible at all.  
  
Test program:  
```  
// clang++ -Wall -Wextra -O2 -g -pthread a.cxx -o a  
  
#include <cstdio>  
#include <boost/process.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <thread>  
  
int main(void) {  
  const std::vector<std::string> argv{"/bin/echo", "foo"};  
  
  boost::asio::io_context iocontext;  
  boost::process::async_pipe pipe{iocontext};  
  
  boost::process::child process{  
    argv,  
    boost::process::std_out > pipe,  
    boost::process::on_exit(  
      [&](int exit_status, const std::error_code &error)  
      {  
        printf("on_exit handler: exit_status=0x%X, error='%s', now: exit_code=0x%X, native_exit_code=0x%X\n",  
          exit_status, error.message().c_str(), process.exit_code(), process.native_exit_code());  
      }  
    ),  
    iocontext  
  };  
  
  std::array<char, 1024> buffer{};  
  
  std::function<void ()> start_read = [&]() {  
    pipe.async_read_some(boost::process::buffer(buffer),  
      [&](boost::system::error_code error, std::size_t transferred) {  
        printf("read handler: error='%s', transferred=%zu, data: '%s'\n",  
          error.message().c_str(), transferred, std::string(buffer.data(), transferred).c_str());  
        if (error) {  
          if (error == boost::asio::error::eof) {  
            printf("read handler: before sleep: exit_code=0x%X, native_exit_code=0x%X\n", process.exit_code(), process.native_exit_code());  
            // Give child process some time to exit, so our next waitpid() will reap it, and the 2nd waitpid() will fail...  
            std::this_thread::sleep_for(std::chrono::seconds(1));  
            printf("read handler: after sleep: exit_code=0x%X, native_exit_code=0x%X\n", process.exit_code(), process.native_exit_code());  
            printf("read handler: process.running()=%i\n", process.running());  
            printf("read handler: after process.running() call: exit_code=0x%X, native_exit_code=0x%X\n", process.exit_code(), process.native_exit_code());  
          }  
          return;  
        }  
        start_read();  
      }  
    );  
  };  
  
  start_read();  
  
  iocontext.run();  
  
  printf("done. exit_code=0x%X, native_exit_code=0x%X\n", process.exit_code(), process.native_exit_code());  
  return 0;  
}  
```  
  
Output (without fix):  
```  
read handler: error='Success', transferred=4, data: 'foo  
'  
read handler: error='End of file', transferred=0, data: ''  
read handler: before sleep: exit_code=0x17F, native_exit_code=0x17F  
read handler: after sleep: exit_code=0x17F, native_exit_code=0x17F  
read handler: process.running()=0  
read handler: after process.running() call: exit_code=0x0, native_exit_code=0x0  
on_exit handler: exit_status=0xFFFFFFFF, error='No child processes', now: exit_code=0xFFFFFFFF, native_exit_code=0xFFFFFFFF  
done. exit_code=0xFFFFFFFF, native_exit_code=0xFFFFFFFF  
```  
  
Output (with fix):  
```  
read handler: error='Success', transferred=4, data: 'foo  
'  
read handler: error='End of file', transferred=0, data: ''  
read handler: before sleep: exit_code=0x17F, native_exit_code=0x17F  
read handler: after sleep: exit_code=0x17F, native_exit_code=0x17F  
read handler: process.running()=0  
read handler: after process.running() call: exit_code=0x0, native_exit_code=0x0  
on_exit handler: exit_status=0xFFFFFFFF, error='No child processes', now: exit_code=0x0, native_exit_code=0x0  
done. exit_code=0x0, native_exit_code=0x0  
```
