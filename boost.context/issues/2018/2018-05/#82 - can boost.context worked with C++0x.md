# #82 - can boost.context worked with C++0x [Closed]

> Username: taozhijiang  
> Created at: 2018-05-29 03:00:12 UTC  
> Updated at: 2018-06-01 01:17:26 UTC  
> Closed at: 2018-05-29 05:42:41 UTC  
> Url: https://github.com/boostorg/context/issues/82  

Hi  
I am trying to use boost.context in our project (may warp out a simple coroutine interface).  
Because all our product environment is base on CentOS 6.x, the default gcc version is 4.4.7, only support C++0x.  
  
I am wondering:  
1. Can the latest stable boost.context work with C++0x? or what's the last support version can fit with C++0x requirements.  
2. What's the ec_v1 and ec_v2 relations with C++11? I can some CXX11 macro protect them.  
  
Thanks and wait for your reply.

---

## Comment 1

> Username: olk  
> Created at: 2018-05-29 05:42:41 UTC  
> Url: https://github.com/boostorg/context/issues/82#issuecomment-392658808  

> Can the latest stable boost.context work with C++0x?  
  
yes  
   
>  What's the ec_v1 and ec_v2 relations with C++11? I can some CXX11 macro protect them.  
  
I suggest to use class boost::context::fiber instead of execution_context<>.  
fiber, continuation and execution_context<> are abstractions of the context switch facility, that manage the stack, add some checks (for instance prevent resuming an terminated context) etc.  
  
some C++11 features are required:  
  
cxx11_auto_declarations  
cxx11_constexpr  
cxx11_defaulted_functions  
cxx11_final  
cxx11_hdr_thread  
cxx11_hdr_tuple  
cxx11_lambdas  
cxx11_noexcept  
cxx11_nullptr  
cxx11_rvalue_references  
cxx11_template_aliases  
cxx11_thread_local  
cxx11_variadic_templates

---

## Comment 2

> Username: taozhijiang  
> Created at: 2018-05-31 13:17:08 UTC  
> Url: https://github.com/boostorg/context/issues/82#issuecomment-393526565  

I am afraid not.  
  
I notice since v1.61, you introduced execution_context v2, but also introduced a file named detail/disable_overload.hpp, which has 'using' inside, and unfortunately this file was used by all ec types.  
  
So, I think even if I can build boost_context.a  with GCC-4.4.7, I can not use this library with GCC-4.4.7 because I can not get rid of detail/disable_overload.hpp. At this point maybe only v1.60.0 can satisfy my requirements.  
  
Am I right?

---

## Comment 3

> Username: olk  
> Created at: 2018-05-31 13:20:30 UTC  
> Updated at: 2018-05-31 13:20:46 UTC  
> Url: https://github.com/boostorg/context/issues/82#issuecomment-393527534  

For < C++11 you can use the internal API (fcontext-API: make_fcontext/jump_fcontext -> boost::context::detail).  
The public API (e.g. fiber, continuation and execution_context<> ) requires C++11.

---

## Comment 4

> Username: taozhijiang  
> Created at: 2018-06-01 01:17:26 UTC  
> Url: https://github.com/boostorg/context/issues/82#issuecomment-393729673  

Got it. thanks.
