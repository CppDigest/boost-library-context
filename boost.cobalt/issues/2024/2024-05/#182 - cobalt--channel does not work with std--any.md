# #182 - cobalt::channel does not work with std::any [Closed]

> Username: dchansen  
> Created at: 2024-05-24 06:59:05 UTC  
> Updated at: 2024-05-30 13:33:47 UTC  
> Closed at: 2024-05-30 13:33:47 UTC  
> Url: https://github.com/boostorg/cobalt/issues/182  

The cobalt::channel fails to compile with std any, due to an ambiguous overload in the internal circular buffer.  
  
The following code   
  
```cpp  
#include <boost/cobalt.hpp>  
#include <any>  
  
namespace bc = boost::cobalt;  
  
bc::task<void> test(){  
    bc::channel<std::any> c;  
    co_await c.write(42);         
}  
  
```  
produces the error   
  
  
``` bash  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/cobalt/channel.hpp:284,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/cobalt.hpp:12,  
                 from <source>:1:  
/opt/compiler-explorer/libs/boost_1_84_0/boost/cobalt/impl/channel.hpp: In instantiation of 'boost::cobalt::channel<T>::channel(std::size_t, boost::cobalt::executor, std::pmr::memory_resource*) [with T = std::any; std::size_t = long unsigned int; boost::cobalt::executor = boost::asio::any_io_executor]':  
<source>:7:27:   required from here  
    7 |     bc::channel<std::any> c;  
      |                           ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/cobalt/impl/channel.hpp:25:7: error: call of overloaded 'circular_buffer(std::size_t&, std::pmr::memory_resource*&)' is ambiguous  
   25 |     : buffer_(limit, resource), executor_(executor) {}  
      |       ^~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/circular_buffer.hpp:58,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/cobalt/channel.hpp:17:  
/opt/compiler-explorer/libs/boost_1_84_0/boost/circular_buffer/base.hpp:1056:5: note: candidate: 'boost::circular_buffer<T, Alloc>::circular_buffer(size_type, param_value_type, const allocator_type&) [with T = std::any; Alloc = std::pmr::polymorphic_allocator<std::any>; size_type = long unsigned int; param_value_type = const std::any&; allocator_type = std::pmr::polymorphic_allocator<std::any>]'  
 1056 |     circular_buffer(size_type n, param_value_type item, const allocator_type& alloc = allocator_type())  
      |     ^~~~~~~~~~~~~~~  
/opt/compiler-explorer/libs/boost_1_84_0/boost/circular_buffer/base.hpp:1037:14: note: candidate: 'boost::circular_buffer<T, Alloc>::circular_buffer(capacity_type, const allocator_type&) [with T = std::any; Alloc = std::pmr::polymorphic_allocator<std::any>; capacity_type = long unsigned int; allocator_type = std::pmr::polymorphic_allocator<std::any>]'  
 1037 |     explicit circular_buffer(capacity_type buffer_capacity, const allocator_type& alloc = allocator_type())  
      |              ^~~~~~~~~~~~~~~  
Compiler returned: 1  
```  
  
[Godbolt link](https://godbolt.org/z/e89Kd44jx)
