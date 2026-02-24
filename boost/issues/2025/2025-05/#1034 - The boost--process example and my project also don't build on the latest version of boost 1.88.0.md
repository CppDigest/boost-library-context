# #1034 - The boost::process example and my project also don't build on the latest version of boost 1.88.0 [Open]

> Username: igorekRnd  
> Created at: 2025-05-05 10:57:29 UTC  
> Updated at: 2025-05-07 15:05:00 UTC  
> Url: https://github.com/boostorg/boost/issues/1034  

The example is taken from the previous version of boost 1.87.0 (https://www.boost.org/doc/libs/1_87_0/doc/html/process.html), because the latest version 1.88.0 does not contain documentation on Process.  
  
```  
#include <boost/process.hpp>  
  
#include <string>  
#include <iostream>  
  
using namespace boost::process;  
  
int main()  
{  
    ipstream pipe_stream;  
    child c("gcc --version", std_out > pipe_stream);  
  
    std::string line;  
  
    while (pipe_stream && std::getline(pipe_stream, line) && !line.empty())  
        std::cerr << line << std::endl;  
  
    c.wait();  
}  
```  
  
assembly line: `LANG=C g++ test.cpp`  
  
compilation errors:  
  
```  
test.cpp:10:5: error: 'ipstream' was not declared in this scope  
   10 |     ipstream pipe_stream;  
      |     ^~~~~~~~  
test.cpp:11:5: error: 'child' was not declared in this scope  
   11 |     child c("gcc --version", std_out > pipe_stream);  
      |     ^~~~~  
test.cpp:15:12: error: 'pipe_stream' was not declared in this scope  
   15 |     while (pipe_stream && std::getline(pipe_stream, line) && !line.empty())  
      |            ^~~~~~~~~~~  
test.cpp:18:5: error: 'c' was not declared in this scope  
   18 |     c.wait();  
      |     ^  
```

---

## Comment 1

> Username: igorekRnd  
> Created at: 2025-05-07 15:01:37 UTC  
> Url: https://github.com/boostorg/boost/issues/1034#issuecomment-2858944338  

dublicate from https://github.com/boostorg/process/issues/480

---

## Comment 2

> Username: igorekRnd  
> Created at: 2025-05-07 15:04:59 UTC  
> Url: https://github.com/boostorg/boost/issues/1034#issuecomment-2858965825  

To build the project I had to change the code a little:  
  
```  
#define BOOST_PROCESS_VERSION 1  
  
#if BOOST_VERSION < 108800  
#include <boost/process.hpp>  
#else  
#include <boost/process/v1/child.hpp>  
#include <boost/process/v1/io.hpp>  
#include <boost/process/v1/pipe.hpp>  
#include <boost/process/v1/search_path.hpp>  
#endif  
  
namespace bp = boost::process;  
```  
  
In the future I plan to switch to Process::v2
