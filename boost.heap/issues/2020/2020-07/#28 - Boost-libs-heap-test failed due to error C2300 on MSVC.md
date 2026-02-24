# #28 - Boost\libs\heap\test failed due to error C2300 on MSVC [Closed]

> Username: QuellaZhang  
> Created at: 2020-07-31 06:40:20 UTC  
> Updated at: 2020-08-20 14:12:48 UTC  
> Closed at: 2020-08-20 14:12:47 UTC  
> Url: https://github.com/boostorg/heap/issues/28  

**Issue description:**  
Found Boost\libs\heap\test https://github.com/boostorg/boost/commit/a61709cb7383c88a6d9ff0acc3c7d674623e88ad build error C2300 in a future release of MSVC, version https://github.com/boostorg/heap/commit/4843f7af37ef06a0656be64e20206a3e8d1465d0 is passed. Could you please take a look?  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git Boost\src  
2. open a VS 2017 x64 command prompt and browse to Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\heap\test  
  
**ErrorMessage:**  
mutable_heap_test.cpp  
.\boost/heap/detail/heap_node.hpp(145): error C2300: 'boost::heap::detail::parent_pointing_heap_node<binomial_heap_data>': class does not have a destructor called '~Node'  
.\boost/heap/detail/heap_node.hpp(142): note: while compiling class template member function 'void boost::heap::detail::node_disposer<boost::heap::detail::heap_node<value_type,true>,boost::heap::detail::heap_node_base<false>,Alloc>::operator ()(NodeBase *)'  
        with  
        [  
            value_type=binomial_heap_data,  
            Alloc=std::allocator<boost::heap::detail::parent_pointing_heap_node<binomial_heap_data>>,  
            NodeBase=boost::heap::detail::heap_node_base<false>  
        ]  
.\boost/intrusive/list.hpp(754): note: see reference to function template instantiation 'void boost::heap::detail::node_disposer<boost::heap::detail::heap_node<value_type,true>,boost::heap::detail::heap_node_base<false>,Alloc>::operator ()(NodeBase *)' being compiled  
        with  
        [  
            value_type=binomial_heap_data,  
            Alloc=std::allocator<boost::heap::detail::parent_pointing_heap_node<binomial_heap_data>>,  
            NodeBase=boost::heap::detail::heap_node_base<false>  
        ]

---

## Comment 1

> Username: glenfe  
> Created at: 2020-08-17 23:18:13 UTC  
> Url: https://github.com/boostorg/heap/issues/28#issuecomment-675161704  

Thanks. I've created #29.

---

## Comment 2

> Username: glenfe  
> Created at: 2020-08-20 14:12:47 UTC  
> Url: https://github.com/boostorg/heap/issues/28#issuecomment-677691347  

Fixed in develop.
