# #376 - with macOS 11.3 , Xcode 12.5  Full of DEPRECATION warning [Closed]

> Username: markyang59  
> Created at: 2021-04-28 04:29:22 UTC  
> Updated at: 2021-05-19 01:38:32 UTC  
> Closed at: 2021-05-19 01:38:32 UTC  
> Url: https://github.com/boostorg/asio/issues/376  

Just download 1.77 and compiled it with my current project.  
Several errors happened..especially deprecation warnings and some memory allocation problems.  
These did not happen with 1.76 on previous macOS and Xcode  
Below is error messages  
  
  
```  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from config.cpp:1:  
In file included from ./config.h:12:  
In file included from ./fftcontext.h:7:  
In file included from /Users/magingax/projects/lib/mk/mkgc2.h:4:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:165:  
/usr/local/boost/include/boost-1_77/boost/asio/use_future.hpp:133:19: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
      return std::allocator<void>();  
                  ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from config.cpp:1:  
In file included from ./config.h:12:  
In file included from ./fftcontext.h:7:  
In file included from /Users/magingax/projects/lib/mk/mkgc2.h:4:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:165:  
/usr/local/boost/include/boost-1_77/boost/asio/use_future.hpp:56:37: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
template <typename Allocator = std::allocator<void> >  
                                    ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from config.cpp:1:  
In file included from ./config.h:12:  
In file included from ./fftcontext.h:7:  
In file included from /Users/magingax/projects/lib/mk/mkgc2.h:4:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:23:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:21:  
/usr/local/boost/include/boost-1_77/boost/asio/detail/recycling_allocator.hpp:97:30: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  static type get(const std::allocator<T>&) { return type(); }  
                             ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/executor_op.hpp:35:3: note: in instantiation of template class 'boost::asio::detail::get_recycling_allocator<std::__1::allocator<void>, boost::asio::detail::thread_info_base::default_tag>' requested here  
  BOOST_ASIO_DEFINE_HANDLER_ALLOCATOR_PTR(executor_op);  
  ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:280:3: note: expanded from macro 'BOOST_ASIO_DEFINE_HANDLER_ALLOCATOR_PTR'  
  BOOST_ASIO_DEFINE_TAGGED_HANDLER_ALLOCATOR_PTR( \  
  ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:251:47: note: expanded from macro 'BOOST_ASIO_DEFINE_TAGGED_HANDLER_ALLOCATOR_PTR'  
      typedef typename ::boost::asio::detail::get_recycling_allocator< \  
                                              ^  
/usr/local/boost/include/boost-1_77/boost/asio/impl/io_context.hpp:307:16: note: in instantiation of member function 'boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::__1::allocator<void>, boost::asio::detail::scheduler_operation>::ptr::allocate' requested here  
      op::ptr::allocate(allocator_), 0 };  
               ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/execute.hpp:208:39: note: in instantiation of function template specialization 'boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>::execute<boost::asio::detail::executor_function>' requested here  
    return BOOST_ASIO_MOVE_CAST(T)(t).execute(BOOST_ASIO_MOVE_CAST(F)(f));  
                                      ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:908:27: note: in instantiation of function template specialization 'boost::asio::execution::detail::any_executor_base::execute_ex<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
      &any_executor_base::execute_ex<Ex>,  
                          ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:497:19: note: in instantiation of function template specialization 'boost::asio::execution::detail::any_executor_base::target_fns_table<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
    : target_fns_(target_fns_table<Executor>(  
                  ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:1406:7: note: in instantiation of function template specialization 'boost::asio::execution::detail::any_executor_base::any_executor_base<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
    : detail::any_executor_base(  
      ^  
/usr/local/boost/include/boost-1_77/boost/asio/any_io_executor.hpp:156:7: note: in instantiation of function template specialization 'boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0>>>::any_executor<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
    : base_type(BOOST_ASIO_MOVE_CAST(Executor)(e))  
      ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/io_object_impl.hpp:59:7: note: in instantiation of function template specialization 'boost::asio::any_io_executor::any_io_executor<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
      executor_(context.get_executor())  
      ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:184:7: note: in instantiation of function template specialization 'boost::asio::detail::io_object_impl<boost::asio::detail::signal_set_service, boost::asio::any_io_executor>::io_object_impl<boost::asio::io_context>' requested here  
    : impl_(0, 0, context)  
      ^  
/usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:24:40: note: in instantiation of function template specialization 'boost::asio::basic_signal_set<boost::asio::any_io_executor>::basic_signal_set<boost::asio::io_context>' requested here  
    boost::asio::signal_set _signal_set{get_io_context(), SIGCHLD};  
                                       ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from config.cpp:1:  
In file included from ./config.h:12:  
In file included from ./fftcontext.h:7:  
In file included from /Users/magingax/projects/lib/mk/mkgc2.h:4:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:23:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:19:  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:183:48: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  static type get(Handler& handler, const std::allocator<T>&)  
                                               ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/signal_handler.hpp:36:3: note: in instantiation of template class 'boost::asio::detail::get_hook_allocator<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), std::__1::allocator<void>>' requested here  
  BOOST_ASIO_DEFINE_HANDLER_PTR(signal_handler);  
  ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:207:47: note: expanded from macro 'BOOST_ASIO_DEFINE_HANDLER_PTR'  
      typedef typename ::boost::asio::detail::get_hook_allocator< \  
                                              ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/signal_set_service.hpp:161:16: note: in instantiation of member function 'boost::asio::detail::signal_handler<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::asio::any_io_executor>::ptr::allocate' requested here  
      op::ptr::allocate(handler), 0 };  
               ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:561:34: note: in instantiation of function template specialization 'boost::asio::detail::signal_set_service::async_wait<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::asio::any_io_executor>' requested here  
      self_->impl_.get_service().async_wait(  
                                 ^  
/usr/local/boost/include/boost-1_77/boost/asio/async_result.hpp:152:5: note: in instantiation of function template specialization 'boost::asio::basic_signal_set<boost::asio::any_io_executor>::initiate_async_wait::operator()<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
    BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
    ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/config.hpp:134:37: note: expanded from macro 'BOOST_ASIO_MOVE_CAST'  
# define BOOST_ASIO_MOVE_CAST(type) static_cast<type&&>  
                                    ^  
/usr/local/boost/include/boost-1_77/boost/asio/async_result.hpp:365:17: note: in instantiation of function template specialization 'boost::asio::async_result<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), void (boost::system::error_code, int)>::initiate<boost::asio::basic_signal_set<>::initiate_async_wait, (lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
    Signature>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
                ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:529:12: note: in instantiation of function template specialization 'boost::asio::async_initiate<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), void (boost::system::error_code, int), boost::asio::basic_signal_set<>::initiate_async_wait>' requested here  
    return async_initiate<SignalHandler, void (boost::system::error_code, int)>(  
           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from config.cpp:1:  
In file included from ./config.h:12:  
In file included from ./fftcontext.h:7:  
In file included from /Users/magingax/projects/lib/mk/mkgc2.h:4:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:23:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:21:  
/usr/local/boost/include/boost-1_77/boost/asio/detail/recycling_allocator.hpp:97:30: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  static type get(const std::allocator<T>&) { return type(); }  
                             ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:79:5: note: in instantiation of template class 'boost::asio::detail::get_recycling_allocator<std::__1::allocator<void>, boost::asio::detail::thread_info_base::executor_function_tag>' requested here  
    BOOST_ASIO_DEFINE_TAGGED_HANDLER_ALLOCATOR_PTR(  
    ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:251:47: note: expanded from macro 'BOOST_ASIO_DEFINE_TAGGED_HANDLER_ALLOCATOR_PTR'  
      typedef typename ::boost::asio::detail::get_recycling_allocator< \  
                                              ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:41:45: note: in instantiation of member function 'boost::asio::detail::executor_function::impl<boost::asio::detail::binder2<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::system::error_code, int>, std::__1::allocator<void>>::ptr::allocate' requested here  
      detail::addressof(a), impl_type::ptr::allocate(a), 0 };  
                                            ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:607:11: note: in instantiation of function template specialization 'boost::asio::detail::executor_function::executor_function<boost::asio::detail::binder2<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::system::error_code, int>, std::__1::allocator<void>>' requested here  
          function(BOOST_ASIO_MOVE_CAST(F)(f), std::allocator<void>()));  
          ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/execute.hpp:208:39: note: in instantiation of function template specialization 'boost::asio::execution::detail::any_executor_base::execute<boost::asio::detail::binder2<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::system::error_code, int>>' requested here  
    return BOOST_ASIO_MOVE_CAST(T)(t).execute(BOOST_ASIO_MOVE_CAST(F)(f));  
                                      ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_work.hpp:505:19: note: in instantiation of function template specialization 'boost::asio::detail::handler_work_base<boost::asio::any_io_executor, void, boost::asio::io_context, boost::asio::executor, void>::dispatch<boost::asio::detail::binder2<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::system::error_code, int>, (lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
      base1_type::dispatch(function, handler);  
                  ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/signal_handler.hpp:76:9: note: (skipping 2 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
      w.complete(handler, handler.handler_);  
        ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/signal_set_service.hpp:162:21: note: in instantiation of member function 'boost::asio::detail::signal_handler<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::asio::any_io_executor>::signal_handler' requested here  
    p.p = new (p.v) op(handler, io_ex);  
                    ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:561:34: note: in instantiation of function template specialization 'boost::asio::detail::signal_set_service::async_wait<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::asio::any_io_executor>' requested here  
      self_->impl_.get_service().async_wait(  
                                 ^  
/usr/local/boost/include/boost-1_77/boost/asio/async_result.hpp:152:5: note: in instantiation of function template specialization 'boost::asio::basic_signal_set<boost::asio::any_io_executor>::initiate_async_wait::operator()<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
    BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
    ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/config.hpp:134:37: note: expanded from macro 'BOOST_ASIO_MOVE_CAST'  
# define BOOST_ASIO_MOVE_CAST(type) static_cast<type&&>  
                                    ^  
/usr/local/boost/include/boost-1_77/boost/asio/async_result.hpp:365:17: note: in instantiation of function template specialization 'boost::asio::async_result<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), void (boost::system::error_code, int)>::initiate<boost::asio::basic_signal_set<>::initiate_async_wait, (lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
    Signature>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
                ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:529:12: note: in instantiation of function template specialization 'boost::asio::async_initiate<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), void (boost::system::error_code, int), boost::asio::basic_signal_set<>::initiate_async_wait>' requested here  
    return async_initiate<SignalHandler, void (boost::system::error_code, int)>(  
           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
30 warnings generated.  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:20:  
/usr/local/boost/include/boost-1_77/boost/asio/associated_allocator.hpp:72:49: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
template <typename T, typename Allocator = std::allocator<void> >  
                                                ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution.hpp:19:  
/usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:607:53: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
          function(BOOST_ASIO_MOVE_CAST(F)(f), std::allocator<void>()));  
                                                    ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
/usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:172:54: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  basic_system_executor<Blocking, Relationship, std::allocator<void> >  
                                                     ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
/usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:176:28: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
        Relationship, std::allocator<void> >();  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
/usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:445:45: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
    execution::relationship_t::fork_t, std::allocator<void> >  
                                            ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:684:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/impl/system_executor.hpp:21:  
/usr/local/boost/include/boost-1_77/boost/asio/system_context.hpp:40:12: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
      std::allocator<void>  
           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:24:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/basic_datagram_socket.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/basic_socket.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/io_object_impl.hpp:23:  
/usr/local/boost/include/boost-1_77/boost/asio/io_context.hpp:229:36: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  typedef basic_executor_type<std::allocator<void>, 0> executor_type;  
                                   ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:24:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/basic_datagram_socket.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/basic_socket.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/io_object_impl.hpp:23:  
/usr/local/boost/include/boost-1_77/boost/asio/io_context.hpp:855:49: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  BOOST_ASIO_CONSTEXPR basic_executor_type<std::allocator<void>, Bits>  
                                                ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:24:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/basic_datagram_socket.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/basic_socket.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/io_object_impl.hpp:23:  
/usr/local/boost/include/boost-1_77/boost/asio/io_context.hpp:858:37: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
    return basic_executor_type<std::allocator<void>, Bits>(  
                                    ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:24:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/basic_datagram_socket.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/basic_socket.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/io_object_impl.hpp:23:  
/usr/local/boost/include/boost-1_77/boost/asio/io_context.hpp:859:27: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
        io_context_, std::allocator<void>(), bits_);  
                          ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:49:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/compose.hpp:136:  
/usr/local/boost/include/boost-1_77/boost/asio/impl/compose.hpp:332:12: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
      std::allocator<void> >::type allocator_type;  
           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:49:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/compose.hpp:136:  
/usr/local/boost/include/boost-1_77/boost/asio/impl/compose.hpp:336:56: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
      return (get_associated_allocator)(handler_, std::allocator<void>());  
                                                       ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:86:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/executor.hpp:342:  
/usr/local/boost/include/boost-1_77/boost/asio/impl/executor.hpp:168:55: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
    return &detail::global<impl<system_executor, std::allocator<void> > >();  
                                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:86:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/executor.hpp:342:  
/usr/local/boost/include/boost-1_77/boost/asio/impl/executor.hpp:203:14: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
        std::allocator<void>());  
             ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:86:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/executor.hpp:342:  
/usr/local/boost/include/boost-1_77/boost/asio/impl/executor.hpp:209:14: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
        std::allocator<void>());  
             ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:86:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/executor.hpp:342:  
/usr/local/boost/include/boost-1_77/boost/asio/impl/executor.hpp:215:14: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
        std::allocator<void>());  
             ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:86:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/executor.hpp:342:  
/usr/local/boost/include/boost-1_77/boost/asio/impl/executor.hpp:244:31: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  : impl_(impl<Executor, std::allocator<void> >::create(e))  
                              ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:154:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/static_thread_pool.hpp:19:  
/usr/local/boost/include/boost-1_77/boost/asio/thread_pool.hpp:86:36: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  typedef basic_executor_type<std::allocator<void>, 0> executor_type;  
                                   ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:154:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/static_thread_pool.hpp:19:  
/usr/local/boost/include/boost-1_77/boost/asio/thread_pool.hpp:89:36: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  typedef basic_executor_type<std::allocator<void>, 0> scheduler_type;  
                                   ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:154:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/static_thread_pool.hpp:19:  
/usr/local/boost/include/boost-1_77/boost/asio/thread_pool.hpp:393:49: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  BOOST_ASIO_CONSTEXPR basic_executor_type<std::allocator<void>, Bits>  
                                                ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:154:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/static_thread_pool.hpp:19:  
/usr/local/boost/include/boost-1_77/boost/asio/thread_pool.hpp:396:37: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
    return basic_executor_type<std::allocator<void>, Bits>(  
                                    ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:154:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/static_thread_pool.hpp:19:  
/usr/local/boost/include/boost-1_77/boost/asio/thread_pool.hpp:397:21: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
        pool_, std::allocator<void>(), bits_);  
                    ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:156:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/strand.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/strand_executor_service.hpp:170:  
/usr/local/boost/include/boost-1_77/boost/asio/detail/impl/strand_executor_service.hpp:221:12: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
      std::allocator<void>());  
           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:165:  
/usr/local/boost/include/boost-1_77/boost/asio/use_future.hpp:131:19: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
    operator std::allocator<void>() const  
                  ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:165:  
/usr/local/boost/include/boost-1_77/boost/asio/use_future.hpp:138:18: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
    is_same<std::allocator<void>, Allocator>::value,  
                 ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:165:  
/usr/local/boost/include/boost-1_77/boost/asio/use_future.hpp:133:19: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
      return std::allocator<void>();  
                  ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:165:  
/usr/local/boost/include/boost-1_77/boost/asio/use_future.hpp:56:37: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
template <typename Allocator = std::allocator<void> >  
                                    ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:23:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:21:  
/usr/local/boost/include/boost-1_77/boost/asio/detail/recycling_allocator.hpp:97:30: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  static type get(const std::allocator<T>&) { return type(); }  
                             ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/executor_op.hpp:35:3: note: in instantiation of template class 'boost::asio::detail::get_recycling_allocator<std::__1::allocator<void>, boost::asio::detail::thread_info_base::default_tag>' requested here  
  BOOST_ASIO_DEFINE_HANDLER_ALLOCATOR_PTR(executor_op);  
  ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:280:3: note: expanded from macro 'BOOST_ASIO_DEFINE_HANDLER_ALLOCATOR_PTR'  
  BOOST_ASIO_DEFINE_TAGGED_HANDLER_ALLOCATOR_PTR( \  
  ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:251:47: note: expanded from macro 'BOOST_ASIO_DEFINE_TAGGED_HANDLER_ALLOCATOR_PTR'  
      typedef typename ::boost::asio::detail::get_recycling_allocator< \  
                                              ^  
/usr/local/boost/include/boost-1_77/boost/asio/impl/io_context.hpp:307:16: note: in instantiation of member function 'boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::__1::allocator<void>, boost::asio::detail::scheduler_operation>::ptr::allocate' requested here  
      op::ptr::allocate(allocator_), 0 };  
               ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/execute.hpp:208:39: note: in instantiation of function template specialization 'boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>::execute<boost::asio::detail::executor_function>' requested here  
    return BOOST_ASIO_MOVE_CAST(T)(t).execute(BOOST_ASIO_MOVE_CAST(F)(f));  
                                      ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:908:27: note: in instantiation of function template specialization 'boost::asio::execution::detail::any_executor_base::execute_ex<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
      &any_executor_base::execute_ex<Ex>,  
                          ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:497:19: note: in instantiation of function template specialization 'boost::asio::execution::detail::any_executor_base::target_fns_table<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
    : target_fns_(target_fns_table<Executor>(  
                  ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:1406:7: note: in instantiation of function template specialization 'boost::asio::execution::detail::any_executor_base::any_executor_base<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
    : detail::any_executor_base(  
      ^  
/usr/local/boost/include/boost-1_77/boost/asio/any_io_executor.hpp:156:7: note: in instantiation of function template specialization 'boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0>>>::any_executor<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
    : base_type(BOOST_ASIO_MOVE_CAST(Executor)(e))  
      ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/io_object_impl.hpp:59:7: note: in instantiation of function template specialization 'boost::asio::any_io_executor::any_io_executor<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0>>' requested here  
      executor_(context.get_executor())  
      ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:184:7: note: in instantiation of function template specialization 'boost::asio::detail::io_object_impl<boost::asio::detail::signal_set_service, boost::asio::any_io_executor>::io_object_impl<boost::asio::io_context>' requested here  
    : impl_(0, 0, context)  
      ^  
/usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:24:40: note: in instantiation of function template specialization 'boost::asio::basic_signal_set<boost::asio::any_io_executor>::basic_signal_set<boost::asio::io_context>' requested here  
    boost::asio::signal_set _signal_set{get_io_context(), SIGCHLD};  
                                       ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:23:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:19:  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:183:48: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  static type get(Handler& handler, const std::allocator<T>&)  
                                               ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/signal_handler.hpp:36:3: note: in instantiation of template class 'boost::asio::detail::get_hook_allocator<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), std::__1::allocator<void>>' requested here  
  BOOST_ASIO_DEFINE_HANDLER_PTR(signal_handler);  
  ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:207:47: note: expanded from macro 'BOOST_ASIO_DEFINE_HANDLER_PTR'  
      typedef typename ::boost::asio::detail::get_hook_allocator< \  
                                              ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/signal_set_service.hpp:161:16: note: in instantiation of member function 'boost::asio::detail::signal_handler<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::asio::any_io_executor>::ptr::allocate' requested here  
      op::ptr::allocate(handler), 0 };  
               ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:561:34: note: in instantiation of function template specialization 'boost::asio::detail::signal_set_service::async_wait<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::asio::any_io_executor>' requested here  
      self_->impl_.get_service().async_wait(  
                                 ^  
/usr/local/boost/include/boost-1_77/boost/asio/async_result.hpp:152:5: note: in instantiation of function template specialization 'boost::asio::basic_signal_set<boost::asio::any_io_executor>::initiate_async_wait::operator()<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
    BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
    ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/config.hpp:134:37: note: expanded from macro 'BOOST_ASIO_MOVE_CAST'  
# define BOOST_ASIO_MOVE_CAST(type) static_cast<type&&>  
                                    ^  
/usr/local/boost/include/boost-1_77/boost/asio/async_result.hpp:365:17: note: in instantiation of function template specialization 'boost::asio::async_result<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), void (boost::system::error_code, int)>::initiate<boost::asio::basic_signal_set<>::initiate_async_wait, (lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
    Signature>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
                ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:529:12: note: in instantiation of function template specialization 'boost::asio::async_initiate<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), void (boost::system::error_code, int), boost::asio::basic_signal_set<>::initiate_async_wait>' requested here  
    return async_initiate<SignalHandler, void (boost::system::error_code, int)>(  
           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
In file included from main.cpp:1:  
In file included from ./main.h:16:  
In file included from /Users/magingax/projects/lib/mk/mkutil.h:13:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio.hpp:21:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/associated_executor.hpp:22:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/system_executor.hpp:20:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:23:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:19:  
In file included from /usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:21:  
/usr/local/boost/include/boost-1_77/boost/asio/detail/recycling_allocator.hpp:97:30: warning: 'allocator<void>' is deprecated [-Wdeprecated-declarations]  
  static type get(const std::allocator<T>&) { return type(); }  
                             ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:79:5: note: in instantiation of template class 'boost::asio::detail::get_recycling_allocator<std::__1::allocator<void>, boost::asio::detail::thread_info_base::executor_function_tag>' requested here  
    BOOST_ASIO_DEFINE_TAGGED_HANDLER_ALLOCATOR_PTR(  
    ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_alloc_helpers.hpp:251:47: note: expanded from macro 'BOOST_ASIO_DEFINE_TAGGED_HANDLER_ALLOCATOR_PTR'  
      typedef typename ::boost::asio::detail::get_recycling_allocator< \  
                                              ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/executor_function.hpp:41:45: note: in instantiation of member function 'boost::asio::detail::executor_function::impl<boost::asio::detail::binder2<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::system::error_code, int>, std::__1::allocator<void>>::ptr::allocate' requested here  
      detail::addressof(a), impl_type::ptr::allocate(a), 0 };  
                                            ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/any_executor.hpp:607:11: note: in instantiation of function template specialization 'boost::asio::detail::executor_function::executor_function<boost::asio::detail::binder2<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::system::error_code, int>, std::__1::allocator<void>>' requested here  
          function(BOOST_ASIO_MOVE_CAST(F)(f), std::allocator<void>()));  
          ^  
/usr/local/boost/include/boost-1_77/boost/asio/execution/execute.hpp:208:39: note: in instantiation of function template specialization 'boost::asio::execution::detail::any_executor_base::execute<boost::asio::detail::binder2<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::system::error_code, int>>' requested here  
    return BOOST_ASIO_MOVE_CAST(T)(t).execute(BOOST_ASIO_MOVE_CAST(F)(f));  
                                      ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/handler_work.hpp:505:19: note: in instantiation of function template specialization 'boost::asio::detail::handler_work_base<boost::asio::any_io_executor, void, boost::asio::io_context, boost::asio::executor, void>::dispatch<boost::asio::detail::binder2<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::system::error_code, int>, (lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
      base1_type::dispatch(function, handler);  
                  ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/signal_handler.hpp:76:9: note: (skipping 2 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
      w.complete(handler, handler.handler_);  
        ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/signal_set_service.hpp:162:21: note: in instantiation of member function 'boost::asio::detail::signal_handler<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::asio::any_io_executor>::signal_handler' requested here  
    p.p = new (p.v) op(handler, io_ex);  
                    ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:561:34: note: in instantiation of function template specialization 'boost::asio::detail::signal_set_service::async_wait<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), boost::asio::any_io_executor>' requested here  
      self_->impl_.get_service().async_wait(  
                                 ^  
/usr/local/boost/include/boost-1_77/boost/asio/async_result.hpp:152:5: note: in instantiation of function template specialization 'boost::asio::basic_signal_set<boost::asio::any_io_executor>::initiate_async_wait::operator()<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
    BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
    ^  
/usr/local/boost/include/boost-1_77/boost/asio/detail/config.hpp:134:37: note: expanded from macro 'BOOST_ASIO_MOVE_CAST'  
# define BOOST_ASIO_MOVE_CAST(type) static_cast<type&&>  
                                    ^  
/usr/local/boost/include/boost-1_77/boost/asio/async_result.hpp:365:17: note: in instantiation of function template specialization 'boost::asio::async_result<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), void (boost::system::error_code, int)>::initiate<boost::asio::basic_signal_set<>::initiate_async_wait, (lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13)>' requested here  
    Signature>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
                ^  
/usr/local/boost/include/boost-1_77/boost/asio/basic_signal_set.hpp:529:12: note: in instantiation of function template specialization 'boost::asio::async_initiate<(lambda at /usr/local/boost/include/boost-1_77/boost/process/detail/posix/sigchld_service.hpp:119:13), void (boost::system::error_code, int), boost::asio::basic_signal_set<>::initiate_async_wait>' requested here  
    return async_initiate<SignalHandler, void (boost::system::error_code, int)>(  
           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/memory:730:28: note: 'allocator<void>' has been explicitly marked deprecated here  
class _LIBCPP_TEMPLATE_VIS _LIBCPP_DEPRECATED_IN_CXX17 allocator<void>  
                           ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1058:39: note: expanded from macro '_LIBCPP_DEPRECATED_IN_CXX17'  
#  define _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_DEPRECATED  
                                      ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__config:1035:48: note: expanded from macro '_LIBCPP_DEPRECATED'  
#    define _LIBCPP_DEPRECATED __attribute__ ((deprecated))  
                                               ^  
30 warnings generated.  
  
빌드가 완료되었지만, 오류가 발생했습니다..  
The terminal process terminated with exit code: -1.  
  
Terminal will be reused by tasks, press any key to close it.  
```

---

## Comment 1

> Username: mclow  
> Created at: 2021-04-28 04:43:39 UTC  
> Url: https://github.com/boostorg/asio/issues/376#issuecomment-828139197  

I don't see any error messages here - just warnings.

---

## Comment 2

> Username: markyang59  
> Created at: 2021-04-28 05:50:40 UTC  
> Url: https://github.com/boostorg/asio/issues/376#issuecomment-828163899  

But the actual building is failed..binary was not made

---

## Comment 3

> Username: CyrilOleynik  
> Created at: 2021-05-18 18:21:47 UTC  
> Url: https://github.com/boostorg/asio/issues/376#issuecomment-843420216  

Hi, is it planned to clean build warning? @mclow

---

## Comment 4

> Username: chriskohlhoff  
> Created at: 2021-05-18 23:27:08 UTC  
> Updated at: 2021-05-18 23:32:06 UTC  
> Url: https://github.com/boostorg/asio/issues/376#issuecomment-843632407  

libc++ has made exactly the same mistake that Visual C++'s standard library made a couple of years ago (and has since corrected):  
  
https://developercommunity.visualstudio.com/t/boost-asio-reports-stdallocator-is-deprecated-in-c/500588  
  
In a nutshell, use of `allocator<void>` is not deprecated by the C++ standard. Only the explicit template specialisation is deprecated. However, applying a `[[deprecated]]` attribute to the specialisation does the wrong thing because it warns about valid uses.  
  
@mclow Are you be able to use your magic libc++ powers to look into this?  
  
In the meantime, please suppress deprecation warnings in your project settings, i.e. define `_LIBCPP_DISABLE_DEPRECATION_WARNINGS`. Or alternatively, upgrade to latest Xcode and compile with `-std=c++20`.

---

## Comment 5

> Username: mclow  
> Created at: 2021-05-19 01:36:07 UTC  
> Url: https://github.com/boostorg/asio/issues/376#issuecomment-843678887  

This is https://bugs.llvm.org/show_bug.cgi?id=50299

---

## Comment 6

> Username: chriskohlhoff  
> Created at: 2021-05-19 01:38:32 UTC  
> Url: https://github.com/boostorg/asio/issues/376#issuecomment-843679621  

Yep just found that ticket literally a minute before your comment. Spooky :)  
Thanks for the followup, closing this issue now.
