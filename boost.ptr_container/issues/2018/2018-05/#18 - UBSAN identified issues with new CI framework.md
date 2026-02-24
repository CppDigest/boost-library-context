# #18 - UBSAN identified issues with new CI framework [Open]

> Username: jeking3  
> Created at: 2018-05-27 13:15:58 UTC  
> Updated at: 2022-04-25 12:59:09 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/18  

Three issues found so disabling the UBSAN build job for now.  
```  
testing.capture-output ../../bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter.run  
====== BEGIN OUTPUT ======  
../../boost/ptr_container/detail/void_ptr_iterator.hpp:68:49: runtime error: reference binding to null pointer of type 'int'  
    #0 0x410d48 in boost::void_ptr_iterator<std::_List_iterator<void*>, int>::operator*() const (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x410d48)  
    #1 0x4107a9 in int const* boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::list<void*, std::allocator<void*> > >::get_const_pointer<boost::void_ptr_iterator<std::_List_iterator<void*>, int> >(boost::void_ptr_iterator<std::_List_iterator<void*>, int>) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x4107a9)  
    #2 0x40f6dd in void boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::list<void*, std::allocator<void*> > >, boost::heap_clone_allocator>::remove<boost::void_ptr_iterator<std::_List_iterator<void*>, int> >(boost::void_ptr_iterator<std::_List_iterator<void*>, int>) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x40f6dd)  
    #3 0x40decc in void boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::list<void*, std::allocator<void*> > >, boost::heap_clone_allocator>::remove<boost::void_ptr_iterator<std::_List_iterator<void*>, int> >(boost::void_ptr_iterator<std::_List_iterator<void*>, int>, boost::void_ptr_iterator<std::_List_iterator<void*>, int>) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x40decc)  
    #4 0x40c4e3 in boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::list<void*, std::allocator<void*> > >, boost::heap_clone_allocator>::remove_all() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x40c4e3)  
    #5 0x40acd9 in boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::list<void*, std::allocator<void*> > >, boost::heap_clone_allocator>::~reversible_ptr_container() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x40acd9)  
    #6 0x408b75 in boost::ptr_sequence_adapter<boost::nullable<int>, std::list<void*, std::allocator<void*> >, boost::heap_clone_allocator>::~ptr_sequence_adapter() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x408b75)  
    #7 0x408c0b in boost::ptr_list<boost::nullable<int>, boost::heap_clone_allocator, std::allocator<void*> >::~ptr_list() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x408c0b)  
    #8 0x40974f in void test_ptr_inserter_helper<boost::ptr_list<boost::nullable<int>, boost::heap_clone_allocator, std::allocator<void*> > >() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x40974f)  
    #9 0x40650f in test_ptr_inserter() test/ptr_inserter.cpp:73  
    #10 0x40f38f in boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x40f38f)  
    #11 0x537908 in boost::function0<void>::operator()() const ../../boost/function/function_template.hpp:768  
    #12 0x581e66 in boost::detail::forward::operator()() ../../boost/test/impl/execution_monitor.ipp:1310  
    #13 0x5846a2 in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) ../../boost/function/function_template.hpp:138  
    #14 0x584140 in boost::function0<int>::operator()() const ../../boost/function/function_template.hpp:769  
    #15 0x582752 in int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:285  
    #16 0x57e9db in boost::execution_monitor::catch_signals(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:874  
    #17 0x57eccc in boost::execution_monitor::execute(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:1213  
    #18 0x580dd4 in boost::execution_monitor::vexecute(boost::function<void ()> const&) ../../boost/test/impl/execution_monitor.ipp:1319  
    #19 0x573857 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) ../../boost/test/impl/unit_test_monitor.ipp:46  
    #20 0x5354c8 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../boost/test/impl/framework.ipp:786  
    #21 0x534b64 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../boost/test/impl/framework.ipp:734  
    #22 0x534b64 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../boost/test/impl/framework.ipp:734  
    #23 0x5251d0 in boost::unit_test::framework::run(unsigned long, bool) ../../boost/test/impl/framework.ipp:1619  
    #24 0x45e7e1 in boost::unit_test::unit_test_main(boost::unit_test::test_suite* (*)(int, char**), int, char**) ../../boost/test/impl/unit_test_main.ipp:231  
    #25 0x45f287 in main ../../boost/test/impl/unit_test_main.ipp:284  
    #26 0x7f16214b9f44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #27 0x406458  (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_inserter.test/gcc-7/debug/cxxstd-03-iso/ptr_inserter+0x406458)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
```  
testing.capture-output ../../bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array.run  
====== BEGIN OUTPUT ======  
../../boost/ptr_container/detail/void_ptr_iterator.hpp:68:49: runtime error: reference binding to null pointer of type 'struct Base'  
    #0 0x42a6bc in boost::void_ptr_iterator<void**, test::Base>::operator*() const (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x42a6bc)  
    #1 0x433135 in test::Base const* boost::ptr_container_detail::sequence_config<test::Base, boost::ptr_container_detail::ptr_array_impl<void*, 10ul, int> >::get_const_pointer<boost::void_ptr_iterator<void**, test::Base> >(boost::void_ptr_iterator<void**, test::Base>) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x433135)  
    #2 0x432093 in void boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<test::Base, boost::ptr_container_detail::ptr_array_impl<void*, 10ul, int> >, boost::heap_clone_allocator>::remove<boost::void_ptr_iterator<void**, test::Base> >(boost::void_ptr_iterator<void**, test::Base>) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x432093)  
    #3 0x42e854 in void boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<test::Base, boost::ptr_container_detail::ptr_array_impl<void*, 10ul, int> >, boost::heap_clone_allocator>::remove<boost::void_ptr_iterator<void**, test::Base> >(boost::void_ptr_iterator<void**, test::Base>, boost::void_ptr_iterator<void**, test::Base>) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x42e854)  
    #4 0x4292ab in boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<test::Base, boost::ptr_container_detail::ptr_array_impl<void*, 10ul, int> >, boost::heap_clone_allocator>::remove_all() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x4292ab)  
    #5 0x41fe4c in boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<test::Base, boost::ptr_container_detail::ptr_array_impl<void*, 10ul, int> >, boost::heap_clone_allocator>::~reversible_ptr_container() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x41fe4c)  
    #6 0x411439 in boost::ptr_sequence_adapter<test::Base, boost::ptr_container_detail::ptr_array_impl<void*, 10ul, int>, boost::heap_clone_allocator>::~ptr_sequence_adapter() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x411439)  
    #7 0x4114cf in boost::ptr_array<test::Base, 10ul, boost::heap_clone_allocator>::~ptr_array() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x4114cf)  
    #8 0x4209cd in std::auto_ptr<boost::ptr_array<test::Base, 10ul, boost::heap_clone_allocator> >::~auto_ptr() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x4209cd)  
    #9 0x429bc7 in boost::ptr_sequence_adapter<test::Base, boost::ptr_container_detail::ptr_array_impl<void*, 10ul, int>, boost::heap_clone_allocator>::ptr_sequence_adapter<boost::ptr_array<test::Base, 10ul, boost::heap_clone_allocator> >(std::auto_ptr<boost::ptr_array<test::Base, 10ul, boost::heap_clone_allocator> >) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x429bc7)  
    #10 0x420b7f in boost::ptr_array<test::Base, 10ul, boost::heap_clone_allocator>::ptr_array(std::auto_ptr<boost::ptr_array<test::Base, 10ul, boost::heap_clone_allocator> >) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x420b7f)  
    #11 0x411bf3 in void test_array_interface<boost::ptr_array<test::Base, 10ul, boost::heap_clone_allocator>, test::Base, test::Derived_class>() (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x411bf3)  
    #12 0x40703c in test_array() test/ptr_array.cpp:90  
    #13 0x431738 in boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x431738)  
    #14 0x555716 in boost::function0<void>::operator()() const ../../boost/function/function_template.hpp:768  
    #15 0x59fc74 in boost::detail::forward::operator()() ../../boost/test/impl/execution_monitor.ipp:1310  
    #16 0x5a24b0 in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) ../../boost/function/function_template.hpp:138  
    #17 0x5a1f4e in boost::function0<int>::operator()() const ../../boost/function/function_template.hpp:769  
    #18 0x5a0560 in int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:285  
    #19 0x59c7e9 in boost::execution_monitor::catch_signals(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:874  
    #20 0x59cada in boost::execution_monitor::execute(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:1213  
    #21 0x59ebe2 in boost::execution_monitor::vexecute(boost::function<void ()> const&) ../../boost/test/impl/execution_monitor.ipp:1319  
    #22 0x591665 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) ../../boost/test/impl/unit_test_monitor.ipp:46  
    #23 0x5532d6 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../boost/test/impl/framework.ipp:786  
    #24 0x552972 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../boost/test/impl/framework.ipp:734  
    #25 0x552972 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../boost/test/impl/framework.ipp:734  
    #26 0x542fde in boost::unit_test::framework::run(unsigned long, bool) ../../boost/test/impl/framework.ipp:1619  
    #27 0x47c711 in boost::unit_test::unit_test_main(boost::unit_test::test_suite* (*)(int, char**), int, char**) ../../boost/test/impl/unit_test_main.ipp:231  
    #28 0x47d1b7 in main ../../boost/test/impl/unit_test_main.ipp:284  
    #29 0x7f8fcb0b0f44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #30 0x406868  (/home/travis/build/jeking3/boost-root/bin.v2/libs/ptr_container/test/ptr_array.test/gcc-7/debug/cxxstd-03-iso/ptr_array+0x406868)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
```  
testing.capture-output ../../bin.v2/libs/ptr_container/test/serialization.test/gcc-7/debug/cxxstd-03-iso/serialization.run  
====== BEGIN OUTPUT ======  
Segmentation fault (core dumped)  
EXIT STATUS: 139  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2019-05-05 02:39:29 UTC  
> Updated at: 2019-05-05 02:39:52 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/18#issuecomment-489382891  

Here's a more detailed stack trace using commit 601ca9d0f61c2b45498a127368e4c4d471105d70, Boost Docker Development Environment, libubsan1-dbg, `rbreak ^__ubsan_handle_`, dis 2, run:  
```  
Breakpoint 1, 0x0000555555784800 in __ubsan_handle_type_mismatch_v1_abort@plt ()  
(gdb) bt  
#0  0x0000555555784800 in __ubsan_handle_type_mismatch_v1_abort@plt ()  
#1  0x0000555555791815 in boost::void_ptr_iterator<std::_List_iterator<void*>, int>::operator* (this=0x7fffffffc9a8)  
    at ../../boost/ptr_container/detail/void_ptr_iterator.hpp:68  
#2  0x0000555555790e4c in boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::__cxx11::list<void*, std::allocator<void*> > >::get_const_pointer<boost::void_ptr_iterator<std::_List_iterator<void*>, int> > (i=...) at ../../boost/ptr_container/ptr_sequence_adapter.hpp:111  
#3  0x000055555578fa00 in boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::__cxx11::list<void*, std::allocator<void*> > >, boost::heap_clone_allocator>::remove<boost::void_ptr_iterator<std::_List_iterator<void*>, int> > (  
    this=0x7fffffffcaf0, i=...) at ../../boost/ptr_container/detail/reversible_ptr_container.hpp:237  
#4  0x000055555578dd41 in boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::__cxx11::list<void*, std::allocator<void*> > >, boost::heap_clone_allocator>::remove<boost::void_ptr_iterator<std::_List_iterator<void*>, int> > (  
    this=0x7fffffffcaf0, first=..., last=...) at ../../boost/ptr_container/detail/reversible_ptr_container.hpp:244  
#5  0x000055555578be98 in boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::__cxx11::list<void*, std::allocator<void*> > >, boost::heap_clone_allocator>::remove_all (this=0x7fffffffcaf0)  
    at ../../boost/ptr_container/detail/reversible_ptr_container.hpp:205  
#6  0x0000555555789e29 in boost::ptr_container_detail::reversible_ptr_container<boost::ptr_container_detail::sequence_config<boost::nullable<int>, std::__cxx11::list<void*, std::allocator<void*> > >, boost::heap_clone_allocator>::~reversible_ptr_container (this=0x7fffffffcaf0, __in_chrg=<optimized out>)  
    at ../../boost/ptr_container/detail/reversible_ptr_container.hpp:499  
#7  0x000055555578792d in boost::ptr_sequence_adapter<boost::nullable<int>, std::__cxx11::list<void*, std::allocator<void*> >, boost::heap_clone_allocator>::~ptr_sequence_adapter (this=0x7fffffffcaf0, __in_chrg=<optimized out>) at ../../boost/ptr_container/ptr_sequence_adapter.hpp:135  
#8  0x00005555557879b5 in boost::ptr_list<boost::nullable<int>, boost::heap_clone_allocator, std::allocator<void*> >::~ptr_list (this=0x7fffffffcaf0,  
    __in_chrg=<optimized out>) at ../../boost/ptr_container/ptr_list.hpp:37  
#9  0x0000555555788514 in test_ptr_inserter_helper<boost::ptr_list<boost::nullable<int>, boost::heap_clone_allocator, std::allocator<void*> > > ()  
    at test/ptr_inserter.cpp:43  
#10 0x000055555578553e in test_ptr_inserter () at test/ptr_inserter.cpp:73  
#11 0x000055555578f5b2 in boost::detail::function::void_function_invoker0<void (*)(), void>::invoke (function_ptr=...)  
    at ../../boost/function/function_template.hpp:117  
#12 0x0000555555936fc1 in boost::function0<void>::operator() (this=0x555555bf6398) at ../../boost/function/function_template.hpp:763  
#13 0x00005555559a810d in boost::detail::forward::operator() (this=0x7fffffffd918) at ../../boost/test/impl/execution_monitor.ipp:1368  
#14 0x00005555559b0aae in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke (function_obj_ptr=...)  
    at ../../boost/function/function_template.hpp:137  
#15 0x00005555559ad427 in boost::function0<int>::operator() (this=0x7fffffffd910) at ../../boost/function/function_template.hpp:763  
#16 0x00005555559aa3ac in boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) (tr=..., F=...) at ../../boost/test/impl/execution_monitor.ipp:290  
#17 0x00005555559a0017 in boost::execution_monitor::catch_signals(boost::function<int ()> const&) (  
    this=0x555555bdb9c0 <boost::unit_test::unit_test_monitor_t::instance()::the_inst>, F=...) at ../../boost/test/impl/execution_monitor.ipp:879  
#18 0x00005555559a0556 in boost::execution_monitor::execute(boost::function<int ()> const&) (  
    this=0x555555bdb9c0 <boost::unit_test::unit_test_monitor_t::instance()::the_inst>, F=...) at ../../boost/test/impl/execution_monitor.ipp:1277  
#19 0x00005555559a2ed9 in boost::execution_monitor::vexecute(boost::function<void ()> const&) (  
    this=0x555555bdb9c0 <boost::unit_test::unit_test_monitor_t::instance()::the_inst>, F=...) at ../../boost/test/impl/execution_monitor.ipp:1377  
#20 0x000055555598ea1a in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned long) (  
    this=0x555555bdb9c0 <boost::unit_test::unit_test_monitor_t::instance()::the_inst>, func=..., timeout_microseconds=0)  
    at ../../boost/test/impl/unit_test_monitor.ipp:49  
#21 0x00005555559335c5 in boost::unit_test::framework::state::execute_test_tree (  
    this=0x555555bdb700 <boost::unit_test::framework::impl::(anonymous namespace)::s_frk_state()::the_inst>, tu_id=65536, timeout_microseconds=0,  
    p_random_generator=0x7fffffffdcd8) at ../../boost/test/impl/framework.ipp:823  
--Type <RET> for more, q to quit, c to continue without paging--  
#22 0x0000555555931435 in boost::unit_test::framework::state::execute_test_tree (  
    this=0x555555bdb700 <boost::unit_test::framework::impl::(anonymous namespace)::s_frk_state()::the_inst>, tu_id=2, timeout_microseconds=0,  
    p_random_generator=0x7fffffffdfb8) at ../../boost/test/impl/framework.ipp:744  
#23 0x0000555555931435 in boost::unit_test::framework::state::execute_test_tree (  
    this=0x555555bdb700 <boost::unit_test::framework::impl::(anonymous namespace)::s_frk_state()::the_inst>, tu_id=1, timeout_microseconds=0,  
    p_random_generator=0x0) at ../../boost/test/impl/framework.ipp:744  
#24 0x000055555591998d in boost::unit_test::framework::run (id=1, continue_test=true) at ../../boost/test/impl/framework.ipp:1688  
#25 0x0000555555804838 in boost::unit_test::unit_test_main (init_func=0x555555785546 <init_unit_test_suite(int, char**)>, argc=1, argv=0x7fffffffe628)  
    at ../../boost/test/impl/unit_test_main.ipp:248  
#26 0x000055555580586e in main (argc=1, argv=0x7fffffffe628) at ../../boost/test/impl/unit_test_main.ipp:304  
(gdb) f 10  
#10 0x000055555578553e in test_ptr_inserter () at test/ptr_inserter.cpp:73  
73          test_ptr_inserter_helper< boost::ptr_list< boost::nullable<int> > >();  
(gdb) l  
68      }  
69  
70  
71      void test_ptr_inserter()  
72      {  
73          test_ptr_inserter_helper< boost::ptr_list< boost::nullable<int> > >();  
74          test_ptr_inserter_helper< boost::ptr_deque< boost::nullable<int> > >();  
75  
76  
77      }  
(gdb) c  
Continuing.  
../../boost/ptr_container/detail/void_ptr_iterator.hpp:68:49: runtime error: reference binding to null pointer of type 'int'  
[Inferior 1 (process 82) exited with code 01]  
```

---

## Comment 2

> Username: jeking3  
> Created at: 2022-04-24 18:39:34 UTC  
> Updated at: 2022-04-25 12:59:09 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/18#issuecomment-1107895253  

These issues are still present in 1.79 and in develop.  I am going to leave the sanitizer builds in the mix and let them fail until someone can fix it.  I added a suppression for the serializer one we see all over.  It's been reported to the serialization project (years ago).  
  
See: https://github.com/boostorg/ptr_container/actions/runs/2217432462  
  
At line boost/ptr_container/ptr_sequence_adapter.hpp:111 there is `&*i` and if i is nullable that is invalid.
