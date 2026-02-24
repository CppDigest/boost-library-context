# #325 - boost::future handles std::exception differently from std::future on Windows [Open]

> Username: sinall  
> Created at: 2020-08-31 09:05:59 UTC  
> Updated at: 2020-08-31 09:05:59 UTC  
> Url: https://github.com/boostorg/thread/issues/325  

I'm using VC++2019, I use boost::when_all since std::when_all is not available yet. But I find that boost::async will discard std::exception::what() and use 'Unknown exception' or 'std::exception' instead, on the other hand, std::async could handle the message as expected.  
  
```  
#include <iostream>  
  
#define BOOST_THREAD_PROVIDES_FUTURE  
#include <boost/thread/future.hpp>  
  
#include <future>  
  
int main() {  
    auto f1 = boost::async([]{ // the program will print 'my exception' if this line uses 'std::async'.  
        try{  
            throw std::invalid_argument("my exception");  
        }  
        catch (const std::exception& e) {  
            throw e;  
        }  
    });  
  
    try {  
        f1.get();  
    }  
    catch (const std::exception& e) {  
        std::cout << e.what() << std::endl;  
    }  
}  
  
```
