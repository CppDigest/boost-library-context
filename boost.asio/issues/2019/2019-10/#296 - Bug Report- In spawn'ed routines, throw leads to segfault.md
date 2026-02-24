# #296 - Bug Report: In spawn'ed routines, throw leads to segfault [Closed]

> Username: nelsonleezonhat  
> Created at: 2019-10-29 04:31:47 UTC  
> Updated at: 2020-12-30 01:10:55 UTC  
> Closed at: 2020-12-30 01:10:54 UTC  
> Url: https://github.com/boostorg/asio/issues/296  

boost-1.71.0  
Gentoo Linux  
x86 **32 bit**  
gcc: 9.2.0  
  
Source code:  
```C++  
#include <iostream>  
#include <memory>  
#include <string>  
#include <stdexcept>  
#include <boost/asio.hpp>  
#include <boost/asio/spawn.hpp>  
  
void test2()  
{  
        std::cerr << "test2" << std::endl;  
        try  
        {  
                throw std::runtime_error("hello");  
        }  
        catch (const std::exception&)  
        {  
                std::cerr << "caught test2" << std::endl;  
                throw;  
        }  
        std::cerr << "never" << std::endl;  
}  
  
void test()  
{  
        std::cerr << "test" << std::endl;  
        try  
        {  
                test2();  
        }  
        catch(const std::exception&)  
        {  
                std::cerr << "caught test" << std::endl;  
        }  
        std::cerr << "all good" << std::endl;  
}  
  
int main(int argc, char* argv[])  
{  
        std::cerr << "main" << std::endl;  
        test();  
        std::cerr << "spawn" << std::endl;  
        boost::asio::io_context ios;  
        boost::asio::spawn(ios,  
            [](boost::asio::yield_context yield)  
            {  
                test();  
            }  
        );  
        ios.run();  
}  
```  
  
Output:  
```  
main  
test  
test2  
caught test2  
caught test  
all good  
spawn  
test  
test2  
Segmentation fault  
```

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:10:53 UTC  
> Url: https://github.com/boostorg/asio/issues/296#issuecomment-752293153  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#728](https://github.com/chriskohlhoff/asio/issues/728).
