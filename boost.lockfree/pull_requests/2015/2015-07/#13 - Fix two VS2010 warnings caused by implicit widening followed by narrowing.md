# #13 Fix two VS2010 warnings caused by implicit widening followed by narrowing [Merged]

> Username: cdglove  
> Created at: 2015-07-22 16:15:12 UTC  
> Updated at: 2015-07-22 19:58:05 UTC  
> Merged at: 2015-07-22 19:58:05 UTC  
> Closed at: 2015-07-22 19:58:05 UTC  
> Url: https://github.com/boostorg/lockfree/pull/13  

This change fixes the following warnings triggered by lockfree;  
  
boost/lockfree/detail/freelist.hpp(299) : warning C4365: 'initializing' : conversion from 'int' to 'boost::lockfree::detail::tagged_index::tag_t', signed/unsigned mismatch  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(59) : warning C4242: '=' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr::tag_t', possible loss of data  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(59) : warning C4365: '=' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr::tag_t', signed/unsigned mismatch  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(135) : warning C4365: 'initializing' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr::tag_t', signed/unsigned mismatch  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(59) : warning C4242: '=' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr::tag_t', possible loss of data  
boost/lockfree/detail/tagged_ptr_ptrcompression.hpp(59) : warning C4365: '=' : conversion from 'int' to 'boost::lockfree::detail::tagged_ptr::tag_t', signed/unsigned mismatch  
  
This can be reproduced with the following minimal test;  
  
// Compile with cl.exe /Wall /I%BOOST_ROOT% /EHsc /DBOOST_ALL_NO_LIB  
# include  
  
int main()  
{  
  boost::lockfree::stack s(32);  
  return 0;  
}

---

## Comment 1

> Username: cdglove  
> Created_at: 2015-07-22 16:15:42 UTC  
> Url: https://github.com/boostorg/lockfree/pull/13#issuecomment-123776739  

Apologies for having to recreate the pull request.  Somehow I messed up my depot state and had to recreate it.

---
