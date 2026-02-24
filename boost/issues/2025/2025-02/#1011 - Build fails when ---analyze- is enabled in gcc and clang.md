# #1011 - Build fails when "--analyze" is enabled in gcc and clang. [Open]

> Username: AiraYumi  
> Created at: 2025-02-18 04:32:52 UTC  
> Updated at: 2025-02-18 04:32:52 UTC  
> Url: https://github.com/boostorg/boost/issues/1011  

When I enable "--analyze" in gcc or clang, the build fails.  
In this case, should I change the source code I'm using? Should I apply a patch to the library?  
  
~~~  
In function ‘boost::fibers::stack_allocator_wrapper boost::fibers::make_stack_allocator_wrapper(Args&& ...) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Args = {}]’:  
cc1plus: error: use of possibly-NULL ‘operator new(16)’ where non-null expected [CWE-690] [-Werror=analyzer-possible-null-argument]  
  ‘boost::fibers::stack_allocator_wrapper boost::fibers::make_stack_allocator_wrapper(Args&& ...) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Args = {}]’: event 1  
    |  
    |/include/boost/fiber/stack_allocator_wrapper.hpp:89:17:  
    |   89 |                 new detail::polymorphic_stack_allocator_impl<StackAllocator>(std::forward< Args >( args) ... )));  
    |      |                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
    |      |                 |  
    |      |                 (1) this call could return NULL  
    |  
  ‘boost::fibers::stack_allocator_wrapper boost::fibers::make_stack_allocator_wrapper(Args&& ...) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Args = {}]’: event 2  
    |  
    |cc1plus:  
    | (2): argument 'this' (‘operator new(16)’) from (1) could be NULL where non-null expected  
    |  
      
note: argument 'this' of ‘boost::fibers::detail::polymorphic_stack_allocator_impl<StackAllocator>::polymorphic_stack_allocator_impl(Args&& ...) [with Args = {}; StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>]’ must be non-null  
   35 |     polymorphic_stack_allocator_impl( Args && ... args )  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
~~~
