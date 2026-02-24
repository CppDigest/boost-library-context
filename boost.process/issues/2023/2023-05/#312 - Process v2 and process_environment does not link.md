# #312 - Process v2 and process_environment does not link. [Closed]

> Username: pixel27  
> Created at: 2023-05-07 14:48:42 UTC  
> Updated at: 2023-05-08 13:12:16 UTC  
> Closed at: 2023-05-08 13:12:15 UTC  
> Url: https://github.com/boostorg/process/issues/312  

Tested on Linux, versions 1.81 and 1.82.  
  
I get a link error when trying to use process_environment:  
  
```undefined reference to boost::process::v2::process_environment::on_setup(boost::process::v2::posix::default_launcher&, boost::filesystem::path const&, char const* const*)```  
  
I've used "nm -D libboost*.so | less" and searched  for "on_setup"  and "process_environment" to try to figure out which lib I need to link with, but maybe the name is mangled to make that useless.   
  
Code to reproduce:  
```  
#include <unordered_map>  
  
#include <boost/asio/io_context.hpp>  
#include <boost/process/v2/environment.hpp>  
#include <boost/process/v2/process.hpp>  
  
int main() {  
    std::unordered_map< boost::process::v2::environment::key, boost::process::v2::environment::value> my_env;  
  
    my_env.emplace(std::make_pair("abc", "xyz"));  
  
    boost::asio::io_context context;  
    boost::process::v2::process p(  
        context, "/usr/bin/ls", {}  
        , boost::process::v2::process_environment(my_env)  
    );  
  
    return p.wait();  
}  
```  
Compile with either "clang++ test.cpp" or "g++ test.cpp".  If you comment out the process_environment call then it compiles and runs fine.  
  
When searching for boost and process I ended up on the v2 documentation and didn't realize it was still experimental.  Maybe the solution is switch over to v1.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-05-07 23:27:15 UTC  
> Url: https://github.com/boostorg/process/issues/312#issuecomment-1537566602  

That might be an error on my part. Can you include `boost/process/v2/src.hpp` in one compile unit? I assume linking against process didn't work.

---

## Comment 2

> Username: pixel27  
> Created at: 2023-05-08 00:43:33 UTC  
> Url: https://github.com/boostorg/process/issues/312#issuecomment-1537590039  

Initial results are this error:  
```  
In file included from test.cpp:6:  
/usr/include/boost/process/v2/src.hpp:13:3: error: Do not compile Beast library source with BOOST_BEAST_HEADER_ONLY defined  
# error Do not compile Beast library source with BOOST_BEAST_HEADER_ONLY defined  
```  
I'm getting that on my arch linux based distro which is getting boost (1.81) from the OS.    
  
My Ubuntu LTS which is using boost 1.82 that I installed/compiled (the OS version of boost is old) is getting:  
```  
In file included from test.cpp:6:  
/home/pixel/boost/include/boost/process/v2/src.hpp:13:3: error: Do not compile Process V2 library source with BOOST_PROCESS_V2_HEADER_ONLY defined. You should probably define BOOST_PROCESS_V2_SEPARATE_COMPILATION  
# error Do not compile Process V2 library source with BOOST_PROCESS_V2_HEADER_ONLY defined. You should probably define BOOST_PROCESS_V2_SEPARATE_COMPILATION  
```  
I'll try to figure out how to compile it with the correct defines.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-05-08 09:12:38 UTC  
> Updated at: 2023-05-08 09:12:44 UTC  
> Url: https://github.com/boostorg/process/issues/312#issuecomment-1538032187  

Right, you'll need to define `BOOST_PROCESS_V2_SEPARATE_COMPILATION` for everything using boost.process.v2.

---

## Comment 4

> Username: pixel27  
> Created at: 2023-05-08 13:12:15 UTC  
> Url: https://github.com/boostorg/process/issues/312#issuecomment-1538340374  

That worked, thank you.
