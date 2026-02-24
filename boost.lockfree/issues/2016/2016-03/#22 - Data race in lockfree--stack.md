# #22 - Data race in lockfree::stack [Open]

> Username: li-feng-sc  
> Created at: 2016-03-29 05:53:53 UTC  
> Updated at: 2016-03-29 05:53:53 UTC  
> Url: https://github.com/boostorg/lockfree/issues/22  

Running my program (uses boost 1.60) through Thread Sanitizer reports the following data race:  
  
> WARNING: ThreadSanitizer: data race (pid=23760)  
>   Write of size 2 at 0x7dc400000110 by thread T6:  
>     #0 boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::link_nodes_atomic(boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node_, boost::lockfree::stack<MYDATA_, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node_) /.../include/boost/lockfree/stack.hpp:226 (bin+0x00000079812c)  
>     #1 bool boost::lockfree::stack<MYDATA_, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::do_push<true>(MYDATA\* const&) /.../include/boost/lockfree/stack.hpp:314 (bin+0x000000798034)  
>     #2 boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::bounded_push(MYDATA\* const&) /.../include/boost/lockfree/stack.hpp:302 (bin+0x000000797a20)  
>   
>   Previous read of size 2 at 0x7dc400000110 by thread T5:  
>     #0 boost::lockfree::detail::tagged_index::get_index() const /.../include/boost/lockfree/detail/freelist.hpp:281 (bin+0x000000797e59)  
>     #1 boost::lockfree::detail::fixed_size_freelist<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, boost::lockfree::detail::compiletime_sized_freelist_storage<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, 4096ul> >::allocate_impl() /.../include/boost/lockfree/detail/freelist.hpp:545 (bin+0x0000007982e0)  
>     #2 unsigned short boost::lockfree::detail::fixed_size_freelist<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, boost::lockfree::detail::compiletime_sized_freelist_storage<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, 4096ul> >::allocate<true>() /.../include/boost/lockfree/detail/freelist.hpp:527 (bin+0x0000007981f9)  
>     #3 boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node\* boost::lockfree::detail::fixed_size_freelist<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, boost::lockfree::detail::compiletime_sized_freelist_storage<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, 4096ul> >::construct<true, true, MYDATA*>(MYDATA\* const&) /.../include/boost/lockfree/detail/freelist.hpp:443 (bin+0x000000798071)  
>     #4 bool boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::do_push<true>(MYDATA\* const&) /.../include/boost/lockfree/stack.hpp:310 (bin+0x000000798021)  
>     #5 boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::bounded_push(MYDATA\* const&) /.../include/boost/lockfree/stack.hpp:302 (bin+0x000000797a20)  
  
And another similar one:  
  
> WARNING: ThreadSanitizer: data race (pid=23760)  
>   Write of size 2 at 0x7dc400001190 by thread T5:  
>     #0 boost::lockfree::detail::tagged_index::set_index(unsigned short) /.../include/boost/lockfree/detail/freelist.hpp:286 (bin+0x000000797cdd)  
>     #1 boost::lockfree::detail::fixed_size_freelist<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, boost::lockfree::detail::compiletime_sized_freelist_storage<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, 4096ul> >::deallocate_impl(unsigned short) /.../include/boost/lockfree/detail/freelist.hpp:585 (bin+0x000000805953)  
>     #2 void boost::lockfree::detail::fixed_size_freelist<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, boost::lockfree::detail::compiletime_sized_freelist_storage<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, 4096ul> >::deallocate<true>(unsigned short) /.../include/boost/lockfree/detail/freelist.hpp:573 (bin+0x0000008058d0)  
>     #3 void boost::lockfree::detail::fixed_size_freelist<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, boost::lockfree::detail::compiletime_sized_freelist_storage<boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::node, 4096ul> >::destruct<true>(boost::lockfree::detail::tagged_index) /.../include/boost/lockfree/detail/freelist.hpp:471 (bin+0x00000080589f)  
>     #4 bool boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::consume_oneboost::lockfree::detail::consume_via_copy<MYDATA* >(boost::lockfree::detail::consume_via_copy<MYDATA*>&) /.../include/boost/lockfree/stack.hpp:502 (bin+0x00000080580c)  
>     #5 bool boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::pop<MYDATA*>(MYDATA_&) /.../include/boost/lockfree/stack.hpp:435 (bin+0x0000008056e2)  
>     #6 boost::lockfree::stack<MYDATA_, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::pop(MYDATA*&) /.../include/boost/lockfree/stack.hpp:417 (bin+0x0000008056a0)  
>   
>   Previous read of size 2 at 0x7dc400001190 by thread T6:  
>     #0 bool boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::consume_oneboost::lockfree::detail::consume_via_copy<MYDATA* >(boost::lockfree::detail::consume_via_copy<MYDATA*>&) /.../include/boost/lockfree/stack.hpp:498 (bin+0x000000805797)  
>     #1 bool boost::lockfree::stack<MYDATA*, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::pop<MYDATA*>(MYDATA_&) /.../include/boost/lockfree/stack.hpp:435 (bin+0x0000008056e2)  
>     #2 boost::lockfree::stack<MYDATA_, boost::lockfree::capacity<4096ul>, boost::parameter::void_, boost::parameter::void_>::pop(MYDATA*&) /.../include/boost/lockfree/stack.hpp:417 (bin+0x0000008056a0)  
  
Upon closer examine the offending line,  I can see there is a compare_exchange_weak check immediately following it which throws away the result if the target is modified by other threads. However, the unprotected nonatomic r/w technically is still a data race in C++ language, which causes the behaviour of the program become undefined.
