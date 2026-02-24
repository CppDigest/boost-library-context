# #159 - asio::buffer_cast issue [Closed]

> Username: yangyangjuanjuan  
> Created at: 2018-11-01 05:13:16 UTC  
> Updated at: 2018-11-01 15:11:36 UTC  
> Closed at: 2018-11-01 15:11:35 UTC  
> Url: https://github.com/boostorg/asio/issues/159  

Hi, I have boost::asio 1.12.1. The output of the following function confused me, am I doing anything wrong?  
```  
/////////////////////////////  
#include <iostream>  
#include <vector>  
#include <sstream>  
#include <iomanip>  
#include <boost/asio.hpp>  
  
inline void testSerializeBufferFun(){  
    std::ostringstream type_stream;  
    type_stream << std::setw(4) << 100;  
    std::cout<<"type_stream:"<<type_stream.str()<<std::endl;  
  
    std::ostringstream head_stream;  
    head_stream << std::setw(10) << 92;  
    std::cout<<"head_stream:"<<head_stream.str()<<std::endl;  
  
    std::vector<boost::asio::const_buffer> buffers;  
    buffers.push_back(boost::asio::buffer(type_stream.str()));  
    buffers.push_back(boost::asio::buffer(head_stream.str()));  
  
    auto test = buffers[0];  
    const unsigned char* p1 = boost::asio::buffer_cast<const unsigned char*>(test);  
    std::cout<<"type_stream again:"<<std::string(reinterpret_cast<const char*>(p1))<<std::endl;  
  
    auto test2 = buffers[1];  
    const unsigned char* p2 = boost::asio::buffer_cast<const unsigned char*>(test2);  
    std::cout<<"head_stream again:"<<std::string(reinterpret_cast<const char*>(p2))<<std::endl;  
}  
/////////////////////////  
```  
The output on my machine is   
```  
type_stream: 100  
head_stream:        92  
type_stream again:        92  
head_stream again:        92  
```  
But I think it should be  
```  
type_stream: 100  
head_stream:        92  
type_stream again: 100  
head_stream again:        92  
```

---

## Comment 1

> Username: yangyangjuanjuan  
> Created at: 2018-11-01 15:11:35 UTC  
> Url: https://github.com/boostorg/asio/issues/159#issuecomment-435072224  

I found my problem, type_stream.str() is a temporary object and boost::asio::buffer return a pointer to it. So it's an undefined behavior.
