# #169 - TSan reports data races in unit_test_log_impl::set_checkpoint [Open]

> Username: Kojoley  
> Created at: 2018-10-03 23:23:16 UTC  
> Updated at: 2018-10-03 23:33:58 UTC  
> Url: https://github.com/boostorg/test/issues/169  

On lines https://github.com/boostorg/test/blob/90d577a620e008417af79f5caa25e701eef954bc/include/boost/test/impl/unit_test_log.ipp#L151-L152  
  
<details><summary>The report</summary>  
  
```  
==================  
WARNING: ThreadSanitizer: data race (pid=63609)  
  Write of size 8 at 0x7fa187ab60f8 by thread T2:  
    #0 boost::unit_test::basic_cstring<char const>::operator=(boost::unit_test::basic_cstring<char const> const&) /home/raven/boost-repo/./boost/test/utils/basic_cstring/basic_cstring.hpp:402:13 (libboost_unit_test_framework.so.1.69.0+0x338fd)  
    #1 boost::unit_test::(anonymous namespace)::unit_test_log_impl::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:151:41 (libboost_unit_test_framework.so.1.69.0+0xcde85)  
    #2 boost::unit_test::unit_test_log_t::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:302:18 (libboost_unit_test_framework.so.1.69.0+0xcddc9)  
    #3 do_test_tss() /home/raven/boost-repo/libs/thread/test/test_tss.cpp:180:5 (test_tss+0x4cabf4)  
    #4 boost::detail::thread_data<void (*)()>::run() /home/raven/boost-repo/./boost/thread/detail/thread.hpp:117:17 (test_tss+0x4e68c6)  
    #5 boost::(anonymous namespace)::thread_proxy(void*) /home/raven/boost-repo/libs/thread/src/pthread/thread.cpp:177:34 (libboost_thread.so.1.69.0+0x10e52)  
  
  Previous write of size 8 at 0x7fa187ab60f8 by main thread:  
    #0 boost::unit_test::basic_cstring<char const>::operator=(boost::unit_test::basic_cstring<char const> const&) /home/raven/boost-repo/./boost/test/utils/basic_cstring/basic_cstring.hpp:402:13 (libboost_unit_test_framework.so.1.69.0+0x338fd)  
    #1 boost::unit_test::(anonymous namespace)::unit_test_log_impl::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:151:41 (libboost_unit_test_framework.so.1.69.0+0xcde85)  
    #2 boost::unit_test::unit_test_log_t::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:302:18 (libboost_unit_test_framework.so.1.69.0+0xcddc9)  
    #3 void (anonymous namespace)::timed_test<void (*)()>(void (*)(), int, (anonymous namespace)::execution_monitor::wait_type) /home/raven/boost-repo/libs/thread/test/./util.inl:159:5 (test_tss+0x4cbe65)  
    #4 test_tss::test_method() /home/raven/boost-repo/libs/thread/test/test_tss.cpp:214:5 (test_tss+0x4cbd24)  
    #5 test_tss_invoker() /home/raven/boost-repo/libs/thread/test/test_tss.cpp:212:1 (test_tss+0x4cb90a)  
    #6 boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) /home/raven/boost-repo/./boost/function/function_template.hpp:118:11 (test_tss+0x4ea5cd)  
    #7 boost::function0<void>::operator()() const /home/raven/boost-repo/./boost/function/function_template.hpp:768:14 (libboost_unit_test_framework.so.1.69.0+0x576b8)  
    #8 boost::detail::forward::operator()() /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:1312:32 (libboost_unit_test_framework.so.1.69.0+0x575d8)  
    #9 boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) /home/raven/boost-repo/./boost/function/function_template.hpp:138:18 (libboost_unit_test_framework.so.1.69.0+0x571a0)  
    #10 boost::function0<int>::operator()() const /home/raven/boost-repo/./boost/function/function_template.hpp:768:14 (libboost_unit_test_framework.so.1.69.0+0x50458)  
    #11 int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:286:30 (libboost_unit_test_framework.so.1.69.0+0x4e899)  
    #12 boost::execution_monitor::catch_signals(boost::function<int ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:875:16 (libboost_unit_test_framework.so.1.69.0+0x4bdd3)  
    #13 boost::execution_monitor::execute(boost::function<int ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:1214:16 (libboost_unit_test_framework.so.1.69.0+0x4c061)  
    #14 boost::execution_monitor::vexecute(boost::function<void ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:1321:5 (libboost_unit_test_framework.so.1.69.0+0x4de67)  
    #15 boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) /home/raven/boost-repo/./boost/test/impl/unit_test_monitor.ipp:49:9 (libboost_unit_test_framework.so.1.69.0+0xdc1b7)  
    #16 boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/raven/boost-repo/./boost/test/impl/framework.ipp:789:44 (libboost_unit_test_framework.so.1.69.0+0x694f5)  
    #17 boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/raven/boost-repo/./boost/test/impl/framework.ipp:737:54 (libboost_unit_test_framework.so.1.69.0+0x68c47)  
    #18 boost::unit_test::framework::run(unsigned long, bool) /home/raven/boost-repo/./boost/test/impl/framework.ipp:1631:29 (libboost_unit_test_framework.so.1.69.0+0x5d5c5)  
    #19 boost::unit_test::unit_test_main(bool (*)(), int, char**) /home/raven/boost-repo/./boost/test/impl/unit_test_main.ipp:247:9 (libboost_unit_test_framework.so.1.69.0+0xd8977)  
    #20 main /home/raven/boost-repo/./boost/test/unit_test.hpp:63:12 (test_tss+0x4ca0f2)  
  
  Location is global 'boost::unit_test::(anonymous namespace)::s_log_impl()::the_inst' of size 128 at 0x7fa187ab60b0 (libboost_unit_test_framework.so.1.69.0+0x0000003bf0f8)  
  
  Thread T2 (tid=63612, running) created by thread T1 at:  
    #0 pthread_create <null> (test_tss+0x432846)  
    #1 boost::thread::start_thread_noexcept() /home/raven/boost-repo/libs/thread/src/pthread/thread.cpp:261:25 (libboost_thread.so.1.69.0+0x10d61)  
    #2 boost::thread::start_thread() /home/raven/boost-repo/./boost/thread/detail/thread.hpp:179:16 (test_tss+0x4e62ab)  
    #3 boost::thread::thread<void (* const&)()>(void (* const&)()) /home/raven/boost-repo/./boost/thread/detail/thread.hpp:266:13 (test_tss+0x4eb6f8)  
    #4 thread_detail_anon::indirect_adapter<void (*)()>::operator()() const /home/raven/boost-repo/libs/thread/test/./util.inl:129:27 (test_tss+0x4eb587)  
    #5 boost::detail::thread_data<thread_detail_anon::indirect_adapter<void (*)()> >::run() /home/raven/boost-repo/./boost/thread/detail/thread.hpp:117:17 (test_tss+0x4eb51f)  
    #6 boost::(anonymous namespace)::thread_proxy(void*) /home/raven/boost-repo/libs/thread/src/pthread/thread.cpp:177:34 (libboost_thread.so.1.69.0+0x10e52)  
  
SUMMARY: ThreadSanitizer: data race /home/raven/boost-repo/./boost/test/utils/basic_cstring/basic_cstring.hpp:402:13 in boost::unit_test::basic_cstring<char const>::operator=(boost::unit_test::basic_cstring<char const> const&)  
==================  
==================  
WARNING: ThreadSanitizer: data race (pid=63609)  
  Write of size 8 at 0x7fa187ab6100 by thread T2:  
    #0 boost::unit_test::basic_cstring<char const>::operator=(boost::unit_test::basic_cstring<char const> const&) /home/raven/boost-repo/./boost/test/utils/basic_cstring/basic_cstring.hpp:403:13 (libboost_unit_test_framework.so.1.69.0+0x3393b)  
    #1 boost::unit_test::(anonymous namespace)::unit_test_log_impl::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:151:41 (libboost_unit_test_framework.so.1.69.0+0xcde85)  
    #2 boost::unit_test::unit_test_log_t::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:302:18 (libboost_unit_test_framework.so.1.69.0+0xcddc9)  
    #3 do_test_tss() /home/raven/boost-repo/libs/thread/test/test_tss.cpp:180:5 (test_tss+0x4cabf4)  
    #4 boost::detail::thread_data<void (*)()>::run() /home/raven/boost-repo/./boost/thread/detail/thread.hpp:117:17 (test_tss+0x4e68c6)  
    #5 boost::(anonymous namespace)::thread_proxy(void*) /home/raven/boost-repo/libs/thread/src/pthread/thread.cpp:177:34 (libboost_thread.so.1.69.0+0x10e52)  
  
  Previous write of size 8 at 0x7fa187ab6100 by main thread:  
    #0 boost::unit_test::basic_cstring<char const>::operator=(boost::unit_test::basic_cstring<char const> const&) /home/raven/boost-repo/./boost/test/utils/basic_cstring/basic_cstring.hpp:403:13 (libboost_unit_test_framework.so.1.69.0+0x3393b)  
    #1 boost::unit_test::(anonymous namespace)::unit_test_log_impl::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:151:41 (libboost_unit_test_framework.so.1.69.0+0xcde85)  
    #2 boost::unit_test::unit_test_log_t::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:302:18 (libboost_unit_test_framework.so.1.69.0+0xcddc9)  
    #3 void (anonymous namespace)::timed_test<void (*)()>(void (*)(), int, (anonymous namespace)::execution_monitor::wait_type) /home/raven/boost-repo/libs/thread/test/./util.inl:159:5 (test_tss+0x4cbe65)  
    #4 test_tss::test_method() /home/raven/boost-repo/libs/thread/test/test_tss.cpp:214:5 (test_tss+0x4cbd24)  
    #5 test_tss_invoker() /home/raven/boost-repo/libs/thread/test/test_tss.cpp:212:1 (test_tss+0x4cb90a)  
    #6 boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) /home/raven/boost-repo/./boost/function/function_template.hpp:118:11 (test_tss+0x4ea5cd)  
    #7 boost::function0<void>::operator()() const /home/raven/boost-repo/./boost/function/function_template.hpp:768:14 (libboost_unit_test_framework.so.1.69.0+0x576b8)  
    #8 boost::detail::forward::operator()() /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:1312:32 (libboost_unit_test_framework.so.1.69.0+0x575d8)  
    #9 boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) /home/raven/boost-repo/./boost/function/function_template.hpp:138:18 (libboost_unit_test_framework.so.1.69.0+0x571a0)  
    #10 boost::function0<int>::operator()() const /home/raven/boost-repo/./boost/function/function_template.hpp:768:14 (libboost_unit_test_framework.so.1.69.0+0x50458)  
    #11 int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:286:30 (libboost_unit_test_framework.so.1.69.0+0x4e899)  
    #12 boost::execution_monitor::catch_signals(boost::function<int ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:875:16 (libboost_unit_test_framework.so.1.69.0+0x4bdd3)  
    #13 boost::execution_monitor::execute(boost::function<int ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:1214:16 (libboost_unit_test_framework.so.1.69.0+0x4c061)  
    #14 boost::execution_monitor::vexecute(boost::function<void ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:1321:5 (libboost_unit_test_framework.so.1.69.0+0x4de67)  
    #15 boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) /home/raven/boost-repo/./boost/test/impl/unit_test_monitor.ipp:49:9 (libboost_unit_test_framework.so.1.69.0+0xdc1b7)  
    #16 boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/raven/boost-repo/./boost/test/impl/framework.ipp:789:44 (libboost_unit_test_framework.so.1.69.0+0x694f5)  
    #17 boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/raven/boost-repo/./boost/test/impl/framework.ipp:737:54 (libboost_unit_test_framework.so.1.69.0+0x68c47)  
    #18 boost::unit_test::framework::run(unsigned long, bool) /home/raven/boost-repo/./boost/test/impl/framework.ipp:1631:29 (libboost_unit_test_framework.so.1.69.0+0x5d5c5)  
    #19 boost::unit_test::unit_test_main(bool (*)(), int, char**) /home/raven/boost-repo/./boost/test/impl/unit_test_main.ipp:247:9 (libboost_unit_test_framework.so.1.69.0+0xd8977)  
    #20 main /home/raven/boost-repo/./boost/test/unit_test.hpp:63:12 (test_tss+0x4ca0f2)  
  
  Location is global 'boost::unit_test::(anonymous namespace)::s_log_impl()::the_inst' of size 128 at 0x7fa187ab60b0 (libboost_unit_test_framework.so.1.69.0+0x0000003bf100)  
  
  Thread T2 (tid=63612, running) created by thread T1 at:  
    #0 pthread_create <null> (test_tss+0x432846)  
    #1 boost::thread::start_thread_noexcept() /home/raven/boost-repo/libs/thread/src/pthread/thread.cpp:261:25 (libboost_thread.so.1.69.0+0x10d61)  
    #2 boost::thread::start_thread() /home/raven/boost-repo/./boost/thread/detail/thread.hpp:179:16 (test_tss+0x4e62ab)  
    #3 boost::thread::thread<void (* const&)()>(void (* const&)()) /home/raven/boost-repo/./boost/thread/detail/thread.hpp:266:13 (test_tss+0x4eb6f8)  
    #4 thread_detail_anon::indirect_adapter<void (*)()>::operator()() const /home/raven/boost-repo/libs/thread/test/./util.inl:129:27 (test_tss+0x4eb587)  
    #5 boost::detail::thread_data<thread_detail_anon::indirect_adapter<void (*)()> >::run() /home/raven/boost-repo/./boost/thread/detail/thread.hpp:117:17 (test_tss+0x4eb51f)  
    #6 boost::(anonymous namespace)::thread_proxy(void*) /home/raven/boost-repo/libs/thread/src/pthread/thread.cpp:177:34 (libboost_thread.so.1.69.0+0x10e52)  
  
SUMMARY: ThreadSanitizer: data race /home/raven/boost-repo/./boost/test/utils/basic_cstring/basic_cstring.hpp:403:13 in boost::unit_test::basic_cstring<char const>::operator=(boost::unit_test::basic_cstring<char const> const&)  
==================  
==================  
WARNING: ThreadSanitizer: data race (pid=63609)  
  Write of size 8 at 0x7fa187ab6108 by thread T2:  
    #0 boost::unit_test::(anonymous namespace)::unit_test_log_impl::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:152:41 (libboost_unit_test_framework.so.1.69.0+0xcdea2)  
    #1 boost::unit_test::unit_test_log_t::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:302:18 (libboost_unit_test_framework.so.1.69.0+0xcddc9)  
    #2 do_test_tss() /home/raven/boost-repo/libs/thread/test/test_tss.cpp:180:5 (test_tss+0x4cabf4)  
    #3 boost::detail::thread_data<void (*)()>::run() /home/raven/boost-repo/./boost/thread/detail/thread.hpp:117:17 (test_tss+0x4e68c6)  
    #4 boost::(anonymous namespace)::thread_proxy(void*) /home/raven/boost-repo/libs/thread/src/pthread/thread.cpp:177:34 (libboost_thread.so.1.69.0+0x10e52)  
  
  Previous write of size 8 at 0x7fa187ab6108 by main thread:  
    #0 boost::unit_test::(anonymous namespace)::unit_test_log_impl::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:152:41 (libboost_unit_test_framework.so.1.69.0+0xcdea2)  
    #1 boost::unit_test::unit_test_log_t::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>) /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:302:18 (libboost_unit_test_framework.so.1.69.0+0xcddc9)  
    #2 void (anonymous namespace)::timed_test<void (*)()>(void (*)(), int, (anonymous namespace)::execution_monitor::wait_type) /home/raven/boost-repo/libs/thread/test/./util.inl:159:5 (test_tss+0x4cbe65)  
    #3 test_tss::test_method() /home/raven/boost-repo/libs/thread/test/test_tss.cpp:214:5 (test_tss+0x4cbd24)  
    #4 test_tss_invoker() /home/raven/boost-repo/libs/thread/test/test_tss.cpp:212:1 (test_tss+0x4cb90a)  
    #5 boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) /home/raven/boost-repo/./boost/function/function_template.hpp:118:11 (test_tss+0x4ea5cd)  
    #6 boost::function0<void>::operator()() const /home/raven/boost-repo/./boost/function/function_template.hpp:768:14 (libboost_unit_test_framework.so.1.69.0+0x576b8)  
    #7 boost::detail::forward::operator()() /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:1312:32 (libboost_unit_test_framework.so.1.69.0+0x575d8)  
    #8 boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) /home/raven/boost-repo/./boost/function/function_template.hpp:138:18 (libboost_unit_test_framework.so.1.69.0+0x571a0)  
    #9 boost::function0<int>::operator()() const /home/raven/boost-repo/./boost/function/function_template.hpp:768:14 (libboost_unit_test_framework.so.1.69.0+0x50458)  
    #10 int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:286:30 (libboost_unit_test_framework.so.1.69.0+0x4e899)  
    #11 boost::execution_monitor::catch_signals(boost::function<int ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:875:16 (libboost_unit_test_framework.so.1.69.0+0x4bdd3)  
    #12 boost::execution_monitor::execute(boost::function<int ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:1214:16 (libboost_unit_test_framework.so.1.69.0+0x4c061)  
    #13 boost::execution_monitor::vexecute(boost::function<void ()> const&) /home/raven/boost-repo/./boost/test/impl/execution_monitor.ipp:1321:5 (libboost_unit_test_framework.so.1.69.0+0x4de67)  
    #14 boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) /home/raven/boost-repo/./boost/test/impl/unit_test_monitor.ipp:49:9 (libboost_unit_test_framework.so.1.69.0+0xdc1b7)  
    #15 boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/raven/boost-repo/./boost/test/impl/framework.ipp:789:44 (libboost_unit_test_framework.so.1.69.0+0x694f5)  
    #16 boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/raven/boost-repo/./boost/test/impl/framework.ipp:737:54 (libboost_unit_test_framework.so.1.69.0+0x68c47)  
    #17 boost::unit_test::framework::run(unsigned long, bool) /home/raven/boost-repo/./boost/test/impl/framework.ipp:1631:29 (libboost_unit_test_framework.so.1.69.0+0x5d5c5)  
    #18 boost::unit_test::unit_test_main(bool (*)(), int, char**) /home/raven/boost-repo/./boost/test/impl/unit_test_main.ipp:247:9 (libboost_unit_test_framework.so.1.69.0+0xd8977)  
    #19 main /home/raven/boost-repo/./boost/test/unit_test.hpp:63:12 (test_tss+0x4ca0f2)  
  
  Location is global 'boost::unit_test::(anonymous namespace)::s_log_impl()::the_inst' of size 128 at 0x7fa187ab60b0 (libboost_unit_test_framework.so.1.69.0+0x0000003bf108)  
  
  Thread T2 (tid=63612, running) created by thread T1 at:  
    #0 pthread_create <null> (test_tss+0x432846)  
    #1 boost::thread::start_thread_noexcept() /home/raven/boost-repo/libs/thread/src/pthread/thread.cpp:261:25 (libboost_thread.so.1.69.0+0x10d61)  
    #2 boost::thread::start_thread() /home/raven/boost-repo/./boost/thread/detail/thread.hpp:179:16 (test_tss+0x4e62ab)  
    #3 boost::thread::thread<void (* const&)()>(void (* const&)()) /home/raven/boost-repo/./boost/thread/detail/thread.hpp:266:13 (test_tss+0x4eb6f8)  
    #4 thread_detail_anon::indirect_adapter<void (*)()>::operator()() const /home/raven/boost-repo/libs/thread/test/./util.inl:129:27 (test_tss+0x4eb587)  
    #5 boost::detail::thread_data<thread_detail_anon::indirect_adapter<void (*)()> >::run() /home/raven/boost-repo/./boost/thread/detail/thread.hpp:117:17 (test_tss+0x4eb51f)  
    #6 boost::(anonymous namespace)::thread_proxy(void*) /home/raven/boost-repo/libs/thread/src/pthread/thread.cpp:177:34 (libboost_thread.so.1.69.0+0x10e52)  
  
SUMMARY: ThreadSanitizer: data race /home/raven/boost-repo/./boost/test/impl/unit_test_log.ipp:152:41 in boost::unit_test::(anonymous namespace)::unit_test_log_impl::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>)  
==================  
```  
  
</details>

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-10-03 23:26:24 UTC  
> Url: https://github.com/boostorg/test/issues/169#issuecomment-426836109  

Are you running several test in different threads?

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-10-03 23:29:26 UTC  
> Url: https://github.com/boostorg/test/issues/169#issuecomment-426836673  

It was one of `libs/thread/test//test_tss` or `libs/thread/test//ex_tss`, cannot say how exactly Boost.Test is used there.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-10-03 23:30:03 UTC  
> Url: https://github.com/boostorg/test/issues/169#issuecomment-426836797  

Thanks for the report!

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-10-03 23:33:58 UTC  
> Url: https://github.com/boostorg/test/issues/169#issuecomment-426837479  

`./b2 -j3 variant=debug toolset=clang-7 cxxflags="-fsanitize=thread -fno-sanitize-recover=all" linkflags="-fsanitize=thread" libs/thread/test//test_tss`  
  
The test https://github.com/boostorg/thread/blob/develop/test/test_tss.cpp
