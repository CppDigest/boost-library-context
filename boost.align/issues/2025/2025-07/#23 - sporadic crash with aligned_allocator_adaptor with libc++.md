# #23 - sporadic crash with aligned_allocator_adaptor with libc++ [Open]

> Username: jcelerier  
> Created at: 2025-07-29 18:47:27 UTC  
> Updated at: 2025-07-29 18:47:52 UTC  
> Url: https://github.com/boostorg/align/issues/23  

I have a `std::vector<boost::aligned_allocator_adaptor<QPointF, 32>>` which occasionnally crashes on destruction.   
QPointF is basically `struct { double x, y; }`   
  
    #0 0x7b185afe6c1f in boost::alignment::aligned_allocator_adaptor<std::__1::allocator<QPointF>, 32ul>::deallocate(QPointF*, unsigned long) /usr/include/boost/align/aligned_allocator_adaptor.hpp:144:22  
    #1 0x7b185afe645c in std::__1::allocator_traits<boost::alignment::aligned_allocator_adaptor<std::__1::allocator<QPointF>, 32ul>>::deallocate[abi:ne200100](boost::alignment::aligned_allocator_adaptor<std::__1::allocator<QPointF>, 32ul>&, QPointF*, unsigned long) /usr/bin/../include/c++/v1/__memory/allocator_traits.h:302:9  
    #2 0x7b185afe623f in std::__1::vector<QPointF, boost::alignment::aligned_allocator_adaptor<std::__1::allocator<QPointF>, 32ul>>::__destroy_vector::operator()[abi:ne200100]() /usr/bin/../include/c++/v1/__vector/vector.h:250:9  
    #3 0x7b185afe49fa in std::__1::vector<QPointF, boost::alignment::aligned_allocator_adaptor<std::__1::allocator<QPointF>, 32ul>>::~vector[abi:ne200100]() /usr/bin/../include/c++/v1/__vector/vector.h:259:67  
  
ASAN stack trace looks like this ^  
  
normal std::allocator works fine, as well as boost::aligned_allocator directly  
  
boost 1.88
