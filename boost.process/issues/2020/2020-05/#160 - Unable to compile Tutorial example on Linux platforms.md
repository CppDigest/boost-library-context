# #160 - Unable to compile Tutorial example on  Linux platforms [Open]

> Username: Hackeet-Paris  
> Created at: 2020-05-22 09:13:56 UTC  
> Updated at: 2020-05-22 09:15:03 UTC  
> Url: https://github.com/boostorg/process/issues/160  

boost version : 1.72  
  
I have some troubles compiling the following program on Linux platforms (Raspberry Pi / Linux Debian 10)  
  
```  
#include <boost/process.hpp>  
#include <iostream>  
  
using namespace boost::process;  
  
int main()  
{  
    ipstream pipe_stream;  
    boost::process::child c(boost::process::search_path("g++"));  
  
    std::string line;  
  
    while (pipe_stream && std::getline(pipe_stream, line) && !line.empty())  
        std::cerr << line << std::endl;  
  
    c.wait();  
}  
```  
  
The error is the following:  
```  
/home/pi/Travail/ThirdParties/boost_1_72_0/boost/fusion/container/vector/detail/cpp03/preprocessed/vector10.hpp:41:22: error: use of deleted function ‘boost::process::detail::posix::exe_cmd_init<char>::exe_cmd_init(const boost::process::detail::posix::exe_cmd_init<char>&)’  
             : m0(arg0) {}  
                      ^  
In file included from /home/pi/Travail/ThirdParties/boost_1_72_0/boost/process/detail/basic_cmd.hpp:20:0,  
                 from /home/pi/Travail/ThirdParties/boost_1_72_0/boost/process/args.hpp:33,  
                 from /home/pi/Travail/ThirdParties/boost_1_72_0/boost/process.hpp:22,  
                 from ../src/main.cpp:1:  
/home/pi/Travail/ThirdParties/boost_1_72_0/boost/process/detail/posix/basic_cmd.hpp:106:5: note: declared here  
     exe_cmd_init(const exe_cmd_init & ) = delete;  
     ^~~~~~~~~~~~  
src/subdir.mk:18: recipe for target 'src/main.o' failed  
make: *** [src/main.o] Error 1  
```  
  
It seems that changing exec_cmd_init default constructor declaration from` exe_cmd_init(const exe_cmd_init & ) = delete; `to `exe_cmd_init(const exe_cmd_init & ) = default;` (line 106 of file /boost/process/detail/posix/basic_cmd.hpp) fixes the compilation issue.
