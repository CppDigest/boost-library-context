# #125 - Bug Report: throw leads to segfault [Closed]

> Username: nelsonleezonhat  
> Created at: 2019-10-29 06:25:03 UTC  
> Updated at: 2019-10-29 10:30:58 UTC  
> Closed at: 2019-10-29 07:13:14 UTC  
> Url: https://github.com/boostorg/context/issues/125  

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

> Username: olk  
> Created at: 2019-10-29 07:13:14 UTC  
> Url: https://github.com/boostorg/context/issues/125#issuecomment-547287582  

it's boost.asio releated - please report it to the asio library

---

## Comment 2

> Username: nelsonleezonhat  
> Created at: 2019-10-29 10:30:58 UTC  
> Url: https://github.com/boostorg/context/issues/125#issuecomment-547355599  

> it's boost.asio releated - please report it to the asio library  
  
I knew that. But Asio internally uses Context. It's very likely it's due to your code.
