# #12 Fix two VS2010 warnings caused by implicit widening followed by narrowing [Closed]

> Username: cdglove  
> Created at: 2015-07-22 16:00:15 UTC  
> Updated at: 2015-07-22 16:00:43 UTC  
> Closed at: 2015-07-22 16:00:43 UTC  
> Url: https://github.com/boostorg/lockfree/pull/12  

This change fixes the following warnings triggered by lockfree;  
  
boost/lockfree/detail/freelist.hpp(299) : warning C4365: 'initializing' : conversion from 'int' to 'boost::lockfree::detail::tagged_index::tag_t', signed/unsigned mismatch  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(59) : warning C4242: '=' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr<T>::tag_t', possible loss of data  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(59) : warning C4365: '=' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr<T>::tag_t', signed/unsigned mismatch  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(135) : warning C4365: 'initializing' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr<T>::tag_t', signed/unsigned mismatch  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(59) : warning C4242: '=' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr<T>::tag_t', possible loss of data  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(59) : warning C4365: '=' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr<T>::tag_t', signed/unsigned mismatch  
  
This can be reproduced with the following minimal test;  
  
// Compile with cl.exe /Wall /I%BOOST_ROOT% /EHsc /DBOOST_ALL_NO_LIB  
# include <boost/lockfree/stack.hpp>  
  
int main()  
{  
    boost::lockfree::stack<int> s(32);  
    return 0;  
}

---
