# #30 - Boost 1.81.0 test segfault [Closed]

> Username: sgn  
> Created at: 2022-12-15 04:02:58 UTC  
> Updated at: 2025-06-01 23:27:19 UTC  
> Closed at: 2025-06-01 23:27:19 UTC  
> Url: https://github.com/boostorg/contract/issues/30  

Running Boost.Contract Test Suite in Boost 1.81.0 run into segfault with this backtrace:  
```  
Reading symbols from ./bin.v2/libs/contract/test/disable-no_post_except_lib.test/gcc-12/release/link-static/threading-multi/visibility-hidden/disable-no_post_except_lib...  
(gdb) r  
Starting program: /builddir/boost-1.81.0/bin.v2/libs/contract/test/disable-no_post_except_lib.test/gcc-12/release/link-static/threading-multi/visibility-hidden/disable-no_post_except_lib   
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/usr/lib64/libthread_db.so.1".  
f::pre  
f::old  
f::body  
f::post  
x::pre  
  
Program received signal SIGSEGV, Segmentation fault.  
0x000055555555c335 in boost::function0<void>::operator() (this=0x555555583258) at ./boost/function/function_template.hpp:763  
763           return get_vtable()->invoker  
(gdb) bt  
#0  0x000055555555c335 in boost::function0<void>::operator() (this=0x555555583258) at ./boost/function/function_template.hpp:763  
#1  boost::contract::detail::cond_base::copy_old (this=0x555555583220) at ./boost/contract/detail/condition/cond_base.hpp:107  
#2  boost::contract::detail::function::init (this=0x555555583220) at ./boost/contract/detail/operation/function.hpp:52  
#3  0x000055555555d3a4 in boost::contract::detail::cond_base::initialize (this=0x555555583220) at ./boost/contract/detail/condition/cond_base.hpp:55  
#4  boost::contract::check::check (contract=..., this=0x7fffffffe7f0) at ./boost/contract/check.hpp:283  
#5  x () at libs/contract/test/disable/lib_x.cpp:25  
#6  0x0000555555559997 in main () at libs/contract/test/disable/lib_xy.hpp:49  
```  
  
Can you provide some help?

---

## Comment 1

> Username: jeking3  
> Created at: 2025-06-01 13:33:09 UTC  
> Url: https://github.com/boostorg/contract/issues/30#issuecomment-2927238057  

This duplicates #36
