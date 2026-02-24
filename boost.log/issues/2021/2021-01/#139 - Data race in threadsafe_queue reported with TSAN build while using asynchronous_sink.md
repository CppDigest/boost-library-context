# #139 - Data race in threadsafe_queue reported with TSAN build while using asynchronous_sink. [Closed]

> Username: vladyslavkazachenko  
> Created at: 2021-01-25 16:34:05 UTC  
> Updated at: 2021-09-06 10:11:01 UTC  
> Closed at: 2021-01-27 23:43:47 UTC  
> Url: https://github.com/boostorg/log/issues/139  

When threadsafe_queue is empty, call push() and try_pop() from different thread causes a data race:  
WARNING: ThreadSanitizer: data race (pid=17538)  
  Write of size 8 at 0x7b0800000340 by main thread (mutexes: write M100, write M90):  
    #0 boost::log::v2s_mt_posix::aux::threadsafe_queue_impl_generic::push(boost::log::v2s_mt_posix::aux::threadsafe_queue_impl::node_base*) boost_1_70_0/libs/log/src/threadsafe_queue.cpp:122   
    #1 boost::log::v2s_mt_posix::aux::threadsafe_queue_impl_generic::push(boost::log::v2s_mt_posix::aux::threadsafe_queue_impl::node_base*) boost_1_70_0/libs/log/src/threadsafe_queue.cpp:100   
    #2 boost::log::v2s_mt_posix::aux::threadsafe_queue<boost::log::v2s_mt_posix::record_view, std::allocator<void> >::push(boost::log::v2s_mt_posix::record_view const&) boost_1_70_0/include/boost/log/detail/threadsafe_queue.hpp:231   
    #3 boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue::enqueue(boost::log::v2s_mt_posix::record_view const&)  boost_1_70_0/include/boost/log/sinks/unbounded_fifo_queue.hpp:83   
    #4 boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue::try_enqueue(boost::log::v2s_mt_posix::record_view const&) boost_1_70_0/include/boost/log/sinks/unbounded_fifo_queue.hpp:91   
    #5 boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue>::try_consume(boost::log::v2s_mt_posix::record_view const&) boost_1_70_0/include/boost/log/sinks/async_frontend.hpp:350   
    #6 boost::log::v2s_mt_posix::core::push_record_move(boost::log::v2s_mt_posix::record&) boost_1_70_0/libs/log/src/core.cpp:694   
    #7 boost::log::v2s_mt_posix::sources::basic_logger<char, boost::log::v2s_mt_posix::sources::severity_logger_mt<...>, boost::log::v2s_mt_posix::sources::multi_thread_model<boost::log::v2s_mt_posix::aux::light_rw_mutex> >::push_record_unlocked(boost::log::v2s_mt_posix::record&&) boost_1_70_0/include/boost/log/core/core.hpp:308   
    #8 boost::log::v2s_mt_posix::sources::basic_logger<char, boost::log::v2s_mt_posix::sources::severity_logger_mt<...>, boost::log::v2s_mt_posix::sources::multi_thread_model<boost::log::v2s_mt_posix::aux::light_rw_mutex> >::push_record_unlocked(boost::log::v2s_mt_posix::record&&) boost_1_70_0/include/boost/log/sources/basic_logger.hpp:267   
    #9 boost::log::v2s_mt_posix::sources::basic_composite_logger<char, boost::log::v2s_mt_posix::sources::severity_logger_mt<...>, boost::log::v2s_mt_posix::sources::multi_thread_model<boost::log::v2s_mt_posix::aux::light_rw_mutex>, boost::log::v2s_mt_posix::sources::features<boost::log::v2s_mt_posix::sources::severity<...> > >::push_record(boost::log::v2s_mt_posix::record&&) boost_1_70_0/include/boost/log/sources/basic_logger.hpp:472  
  
Previous read of size 8 at 0x7b0800000340 by thread T1 (mutexes: write M89):  
    #0 boost::log::v2s_mt_posix::aux::threadsafe_queue_impl_generic::try_pop(boost::log::v2s_mt_posix::aux::threadsafe_queue_impl::node_base*&, boost::log::v2s_mt_posix::aux::threadsafe_queue_impl::node_base*&) boost_1_70_0/libs/log/src/threadsafe_queue.cpp:126   
    #1 boost::log::v2s_mt_posix::aux::threadsafe_queue_impl_generic::try_pop(boost::log::v2s_mt_posix::aux::threadsafe_queue_impl::node_base*&, boost::log::v2s_mt_posix::aux::threadsafe_queue_impl::node_base*&) boost_1_70_0/libs/log/src/threadsafe_queue.cpp:107   
    #2 boost::log::v2s_mt_posix::aux::threadsafe_queue<boost::log::v2s_mt_posix::record_view, std::allocator<void> >::try_pop(boost::log::v2s_mt_posix::record_view&) boost_1_70_0/include/boost/log/detail/threadsafe_queue.hpp:245   
    #3 boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue::dequeue_ready(boost::log::v2s_mt_posix::record_view&) boost_1_70_0/include/boost/log/sinks/unbounded_fifo_queue.hpp:111   
    #4 boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue>::run() boost_1_70_0/include/boost/log/sinks/async_frontend.hpp:378   
    #5 boost::_mfi::mf0<void, boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue> >::operator()(boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue>*) const boost_1_70_0/include/boost/bind/mem_fn_template.hpp:49   
    #6 void boost::_bi::list1<boost::_bi::value<boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue>*> >::operator()<boost::_mfi::mf0<void, boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue> >, boost::_bi::list0>(boost::_bi::type<void>, boost::_mfi::mf0<void, boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue> >&, boost::_bi::list0&, int) boost_1_70_0/include/boost/bind/bind.hpp:259  
    #7 boost::_bi::bind_t<void, boost::_mfi::mf0<void, boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue> >, boost::_bi::list1<boost::_bi::value<boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue>*> > >::operator()() boost_1_70_0/include/boost/bind/bind.hpp:1294   
    #8 boost::detail::thread_data<boost::_bi::bind_t<void, boost::_mfi::mf0<void, boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue> >, boost::_bi::list1<boost::_bi::value<boost::log::v2s_mt_posix::sinks::asynchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::log::v2s_mt_posix::sinks::unbounded_fifo_queue>*> > > >::run() boost_1_70_0/include/boost/thread/detail/thread.hpp:120

---

## Comment 1

> Username: Lastique  
> Created at: 2021-01-27 23:44:12 UTC  
> Url: https://github.com/boostorg/log/issues/139#issuecomment-768652647  

Thanks for the report.

---

## Comment 2

> Username: ghost  
> Created at: 2021-09-06 06:26:05 UTC  
> Url: https://github.com/boostorg/log/issues/139#issuecomment-913378230  

I'm getting tons and tons of very similar if not identical tsan reports in 1.65.1 - would that be the same bug?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-09-06 08:58:43 UTC  
> Url: https://github.com/boostorg/log/issues/139#issuecomment-913473972  

The problem was present in 1.65.1, so yes, it is likely to be flagged by TSAN.

---

## Comment 4

> Username: ghost  
> Created at: 2021-09-06 10:11:01 UTC  
> Url: https://github.com/boostorg/log/issues/139#issuecomment-913525406  

Great, then I don't need to investigate this anymore. What I did to silence them was to swap to synchronized sinks.
