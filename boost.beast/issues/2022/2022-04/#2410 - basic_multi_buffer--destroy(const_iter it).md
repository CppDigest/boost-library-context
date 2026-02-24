# #2410 - basic_multi_buffer::destroy(const_iter it) [Closed]

> Username: ZhenghengLi  
> Created at: 2022-04-20 10:06:08 UTC  
> Updated at: 2022-06-22 19:56:27 UTC  
> Closed at: 2022-06-22 19:56:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2410  

Hi,  
In order to investigate the performance of beast::multi_buffer,  I read most the code of class basic_multi_buffer today, and found a small potential issue about the destroy() method of this class. In implementation of the overload destroy(const_iter it), at line around 1208 of file beast/core/impl/multi_buffer.hpp, does the list_.erase(it) returns the next iterator after the erased item, but not the reference to the erased item (i.e. the element)? If so, this method may only recursively erase the items in the list, leaving the 'element' memory not freed, which will results in memory leak. Is this behavior intended or by mistake? Fortunately, I found that this overload is never been invoked by other methods of this class.  
  
Here is a copy of the implementation code:  
```cpp  
template<class Allocator>  
void  
basic_multi_buffer<Allocator>::  
destroy(const_iter it)  
{  
    auto& e = list_.erase(it);  
    destroy(e);  
}  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-04-20 10:29:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2410#issuecomment-1103776703  

Hmm - if it's never called it begs the question why it exists.  
We should check the coverage reports.

---

## Comment 2

> Username: ZhenghengLi  
> Created at: 2022-04-29 02:52:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2410#issuecomment-1112830680  

I did a simple test. Add a public method to explicitly call the destroy(const_iter it) overload:  
```c++  
void destroy_iter_test() {  
    destroy(list_.begin());  
}     
```  
and write a main function (in file test.cc) as follows:  
```c++  
#include "multi_buffer_test.hpp"  
int main(int argc, char** argv) {  
    boost::beast::multi_buffer buffer;  
    buffer.prepare(100);  
    buffer.destroy_iter_test();  
    return 0;  
}  
```  
where multi_buffer_test.hpp is the modified version of multi_buffer with the added method.  
I found that when  the destroy(const_iter it) overload is called, there will be a compile errer:  
```  
In file included from multi_buffer_test.hpp:598,  
                 from test.cc:1:  
/usr/include/boost/beast/core/impl/multi_buffer.hpp: In instantiation of ‘void boost::beast::basic_multi_buffer<Allocator>::destroy(boost::beast::basic_multi_buffer<Allocator>::const_iter) [with Allocator = std::allocator<char>; boost::beast::basic_multi_buffer<Allocator>::const_iter = boost::intrusive::list_iterator<boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, boost::intrusive::normal_link, boost::intrusive::dft_tag, 1>, true>]’:  
multi_buffer_test.hpp:569:16:   required from ‘void boost::beast::basic_multi_buffer<Allocator>::destroy_iter_test() [with Allocator = std::allocator<char>]’  
test.cc:6:29:   required from here  
/usr/include/boost/beast/core/impl/multi_buffer.hpp:1206:11: error: cannot bind non-const lvalue reference of type ‘boost::intrusive::list_iterator<boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, boost::intrusive::normal_link, boost::intrusive::dft_tag, 1>, false>&’ to an rvalue of type ‘boost::intrusive::list_impl<boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, boost::intrusive::normal_link, boost::intrusive::dft_tag, 1>, long unsigned int, true, void>::iterator’ {aka ‘boost::intrusive::list_iterator<boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, boost::intrusive::normal_link, boost::intrusive::dft_tag, 1>, false>’}  
 1206 |     auto& e = list_.erase(it);  
      |           ^  
```  
This means the overload is never called in the unmodified muti_buffer, otherwise there should be a compile error.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-14 17:34:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2410#issuecomment-1155499439  

We can remove the function if it isn't called
