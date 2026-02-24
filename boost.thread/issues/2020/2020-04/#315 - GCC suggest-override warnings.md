# #315 - GCC suggest-override warnings [Open]

> Username: EugeneZelenko  
> Created at: 2020-04-13 00:28:46 UTC  
> Updated at: 2020-05-19 21:50:31 UTC  
> Url: https://github.com/boostorg/thread/issues/315  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted:  
  
<pre>  
./boost/thread/detail/nullary_function.hpp:158:11: warning: ‘R boost::detail::nullary_function<R()>::impl_type<F>::call() [with F = boost::thread_detail::default_barrier_reseter; R = long unsigned int]’ can be marked override [-Wsuggest-override]  
./boost/thread/detail/nullary_function.hpp:62:14: warning: ‘virtual void boost::detail::nullary_function<void()>::impl_type_ptr::call()’ can be marked override [-Wsuggest-override]  
./boost/thread/detail/thread.hpp:118:18: warning: ‘void boost::detail::thread_data<F>::run() [with F = boost::_bi::bind_t<void, boost::_mfi::mf0<void, boost::log::v2_mt_posix::sinks::asynchronous_sink<boost::log::v2_mt_posix::sinks::basic_text_ostream_backend<char>, boost::log::v2_mt_posix::sinks::unbounded_fifo_queue> >, boost::_bi::list1<boost::_bi::value<boost::log::v2_mt_posix::sinks::asynchronous_sink<boost::log::v2_mt_posix::sinks::basic_text_ostream_backend<char>, boost::log::v2_mt_posix::sinks::unbounded_fifo_queue>*> > >]’ can be marked override [-Wsuggest-override]  
./boost/thread/detail/thread.hpp:804:18: warning: ‘void boost::detail::thread_exit_function<F>::operator()() [with F = boost::_bi::bind_t<boost::_bi::unspecified, boost::checked_deleter<long unsigned int>, boost::_bi::list1<boost::_bi::value<long unsigned int*> > >]’ can be marked override [-Wsuggest-override]  
./boost/thread/future.hpp:319:18: warning: ‘virtual void boost::detail::shared_state<void>::do_continuation(boost::unique_lock<boost::mutex>&)’ can be marked override [-Wsuggest-override]  
./boost/thread/future.hpp:3465:18: warning: ‘virtual void boost::detail::task_shared_state<void (*)(), void>::do_apply()’ can be marked override [-Wsuggest-override]  
./boost/thread/future.hpp:3486:18: warning: ‘virtual void boost::detail::task_shared_state<void (*)(), void>::do_run()’ can be marked override [-Wsuggest-override]  
./boost/thread/future.hpp:947:24: warning: ‘void boost::detail::future_async_shared_state_base<Rp>::wait(boost::unique_lock<boost::mutex>&, bool) [with Rp = void]’ can be marked override [-Wsuggest-override]  
</pre>  
<pre>  
libs/thread/src/future.cpp:23:29: warning: ‘virtual const char* boost::thread_detail::future_error_category::name() const’ can be marked override [-Wsuggest-override]  
libs/thread/src/future.cpp:24:29: warning: ‘virtual std::__cxx11::string boost::thread_detail::future_error_category::message(int) const’ can be marked override [-Wsuggest-override]  
libs/thread/src/pthread/thread.cpp:225:18: warning: ‘virtual void boost::detail::externally_launched_thread::run()’ can be marked override [-Wsuggest-override]  
libs/thread/src/pthread/thread.cpp:227:18: warning: ‘virtual void boost::detail::externally_launched_thread::notify_all_at_thread_exit(boost::condition_variable*, boost::mutex*)’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-05-19 21:50:30 UTC  
> Url: https://github.com/boostorg/thread/issues/315#issuecomment-631102608  

See fixes in #318.
