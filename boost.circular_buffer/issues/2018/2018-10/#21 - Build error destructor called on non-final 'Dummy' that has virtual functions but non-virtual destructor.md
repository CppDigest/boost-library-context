# #21 - Build error destructor called on non-final 'Dummy' that has virtual functions but non-virtual destructor [Closed]

> Username: jeking3  
> Created at: 2018-10-27 21:18:48 UTC  
> Updated at: 2018-10-28 16:12:45 UTC  
> Closed at: 2018-10-28 16:12:45 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/21  

Saw this in a build locally, thought I would report it... I haven't done any analysis on it.  
  
```  
../boost/circular_buffer/allocators.hpp:81:9: warning: destructor called on non-final 'Dummy' that has virtual functions but non-virtual destructor [-Wdelete-non-virtual-dtor]  
        ptr->~U();  
        ^  
../boost/circular_buffer/details.hpp:473:38: note: in instantiation of function template specialization 'boost::cb_details::allocator_traits<std::allocator<Dummy> >::destroy<Dummy>' requested here  
            allocator_traits<Alloc>::destroy(alloc, boost::to_address(next));  
                                     ^  
../boost/circular_buffer/base.hpp:2500:25: note: in instantiation of function template specialization 'boost::cb_details::uninitialized_fill_n_with_alloc<Dummy *, unsigned long, Dummy, std::allocator<Dummy> >' requested here  
            cb_details::uninitialized_fill_n_with_alloc(m_buff, size(), item, alloc());  
                        ^  
../boost/circular_buffer/base.hpp:1056:9: note: in instantiation of member function 'boost::circular_buffer<Dummy, std::allocator<Dummy> >::initialize_buffer' requested here  
        initialize_buffer(n, item);  
        ^  
../libs/circular_buffer/test/base_test.cpp:37:28: note: in instantiation of member function 'boost::circular_buffer<Dummy, std::allocator<Dummy> >::circular_buffer' requested here  
    circular_buffer<Dummy> cb(3, Dummy());  
                           ^  
../boost/circular_buffer/allocators.hpp:81:15: note: qualify call to silence this warning  
        ptr->~U();  
              ^  
              Dummy::  
1 warning generated.  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2018-10-28 16:12:45 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/21#issuecomment-433718984  

Should be resolved in c084535.
