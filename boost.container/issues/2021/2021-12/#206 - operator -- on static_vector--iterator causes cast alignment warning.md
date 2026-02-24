# #206 - operator -> on static_vector::iterator causes cast alignment warning [Closed]

> Username: jtbr  
> Created at: 2021-12-23 13:37:38 UTC  
> Updated at: 2021-12-28 14:17:21 UTC  
> Closed at: 2021-12-28 14:17:21 UTC  
> Url: https://github.com/boostorg/container/issues/206  

Using operator-> on static_vector::iterator, gcc (6.3.1 arm-none-eabi) gives the following warning with -Wcast-align:  
  
```  
boost_1_64_0\boost\move\detail\meta_utils.hpp: In instantiation of 'static T* boost::move_detail::addressof_impl<T>::f(T&, long int) [with T = a_struct]':  
boost_1_64_0\boost\move\detail\meta_utils.hpp:270:7:   required from 'T* boost::move_detail::addressof(T&) [with T = a_struct]'  
boost_1_64_0\boost\intrusive\pointer_traits.hpp:292:48:   required from 'static T* boost::intrusive::pointer_traits<T*>::pointer_to(boost::intrusive::pointer_traits<T*>::reference) [with T = a_struct; boost::intrusive::pointer_traits<T*>::pointer = a_struct*; boost::intrusive::pointer_traits<T*>::reference = a_struct&]'  
boost_1_64_0\boost\container\vector.hpp:126:69:   required from 'boost::container::container_detail::vec_iterator<Pointer, IsConst>::pointer boost::container::container_detail::vec_iterator<Pointer, IsConst>::operator->() const [with Pointer = a_struct*; bool IsConst = false; boost::container::container_detail::vec_iterator<Pointer, IsConst>::pointer = a_struct*]'  
mycode.cpp:12:20:   required from here  
boost_1_64_0\boost\move\detail\meta_utils.hpp:258:14: warning: cast from 'char*' to 'a_struct*' increases required alignment of target type [-Wcast-align]  
       return reinterpret_cast<T*>(  
              ^~~~~~~~~~~~~~~~~~~~~  
          &const_cast<char&>(reinterpret_cast<const volatile char &>(v)));  
          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
De-referencing the iterator before accessing the member gives no warning. Here is a minimal example with the warning:  
```  
#include "boost/container/static_vector.hpp"  
  
struct a_struct {  
    int a = 1;  
};  
void myExample() {  
    typedef boost::container::static_vector<a_struct, 1> vec_type;  
    int something=10;  
    vec_type vec;  
    vec.push_back({2});  
    vec_type::iterator it = vec.begin();  
    something += it->a; // <--- gives the warning  
    something += (*it).a; // <--- gives no warning  
}  
  
```  
I'm using boost 1.64, but nothing about this seems to have changed in the latest code. The same warnings also appear with GCC 11.2.0 (mingw) -Wcast-align=strict.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-12-28 14:15:05 UTC  
> Url: https://github.com/boostorg/container/issues/206#issuecomment-1002126108  

Thanks for the report. Supporting Wcast-align support requires several changes in dependent libraries. I've prepared the following commits to prepare the patch for Boost.Container:  
  
- Move: https://github.com/boostorg/move/commit/7637429b6c361321cf0512014949f86be32a1a85  
  
- Intrusive: https://github.com/boostorg/intrusive/commit/691154b887692187463f7722b08745bacf14db9f
