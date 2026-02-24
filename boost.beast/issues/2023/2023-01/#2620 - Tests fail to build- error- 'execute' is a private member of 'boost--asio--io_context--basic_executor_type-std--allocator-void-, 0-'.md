# #2620 - Tests fail to build: error: 'execute' is a private member of 'boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>' [Closed]

> Username: yurivict  
> Created at: 2023-01-12 04:28:27 UTC  
> Updated at: 2023-01-12 04:35:15 UTC  
> Closed at: 2023-01-12 04:35:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2620  

```  
/usr/local/include/boost/asio/async_result.hpp:896:21: note: in instantiation of function template specialization 'boost::asio::detail::completion_handler_async_result<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>>, void ()>::initiate<boost::asio::detail::initiate_post_with_executor<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>, boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>>>' requested here  
    Signatures...>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
                    ^  
/usr/local/include/boost/asio/impl/post.hpp:257:10: note: in instantiation of function template specialization 'boost::asio::async_initiate<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>>, void (), boost::asio::detail::initiate_post_with_executor<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>>' requested here  
  return async_initiate<NullaryToken, void()>(  
         ^  
/usr/ports/www/boost-beast/work/beast-boost-1.81.0/test/beast/core/bind_handler.cpp:367:14: note: in instantiation of function template specialization 'boost::asio::post<boost::asio::io_context, boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>>>' requested here  
        net::post(ioc, std::forward<F>(f));  
             ^  
/usr/ports/www/boost-beast/work/beast-boost-1.81.0/test/beast/core/bind_handler.cpp:544:13: note: in instantiation of function template specialization 'boost::beast::bind_handler_test::testHooks<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>>>' requested here  
            testHooks(ioc, bind_front_handler(net::bind_executor(  
            ^  
/usr/local/include/boost/asio/io_context.hpp:1024:8: note: declared private here  
  void execute(BOOST_ASIO_MOVE_ARG(Function) f) const;  
       ^  
/usr/ports/www/boost-beast/work/beast-boost-1.81.0/test/beast/core/bind_handler.cpp:225:47: error: 'execute' is a private member of 'boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>'  
                net::execution::allocator(a)).execute(std::forward<F>(f));  
                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~  
/usr/local/include/boost/asio/detail/work_dispatcher.hpp:136:26: note: in instantiation of function template specialization 'boost::beast::bind_handler_test::test_executor::dispatch<boost::asio::detail::binder0<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>>, std::allocator<void>>' requested here  
    work_.get_executor().dispatch(  
                         ^  
/usr/local/include/boost/asio/detail/handler_invoke_helpers.hpp:51:3: note: in instantiation of member function 'boost::asio::detail::work_dispatcher<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>, boost::beast::bind_handler_test::test_executor>::operator()' requested here  
  function();  
  ^  
/usr/local/include/boost/asio/impl/io_context.hpp:294:42: note: in instantiation of function template specialization 'boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::work_dispatcher<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>, boost::beast::bind_handler_test::test_executor>, boost::asio::detail::work_dispatcher<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>, boost::beast::bind_handler_test::test_executor>>' requested here  
      boost_asio_handler_invoke_helpers::invoke(tmp, tmp);  
                                         ^  
/usr/local/include/boost/asio/execution/execute.hpp:208:39: note: in instantiation of function template specialization 'boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>::execute<boost::asio::detail::work_dispatcher<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>, boost::beast::bind_handler_test::test_executor>>' requested here  
    return BOOST_ASIO_MOVE_CAST(T)(t).execute(BOOST_ASIO_MOVE_CAST(F)(f));  
                                      ^  
/usr/local/include/boost/asio/async_result.hpp:482:5: note: in instantiation of function template specialization 'boost::asio::detail::initiate_post_with_executor<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>::operator()<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>>' requested here  
    BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
    ^  
/usr/local/include/boost/asio/detail/config.hpp:135:37: note: expanded from macro 'BOOST_ASIO_MOVE_CAST'  
# define BOOST_ASIO_MOVE_CAST(type) static_cast<type&&>  
                                    ^  
/usr/local/include/boost/asio/async_result.hpp:896:21: note: in instantiation of function template specialization 'boost::asio::detail::completion_handler_async_result<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>, void ()>::initiate<boost::asio::detail::initiate_post_with_executor<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>, boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>>' requested here  
    Signatures...>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
                    ^  
/usr/local/include/boost/asio/impl/post.hpp:257:10: note: in instantiation of function template specialization 'boost::asio::async_initiate<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>, void (), boost::asio::detail::initiate_post_with_executor<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>>' requested here  
  return async_initiate<NullaryToken, void()>(  
         ^  
/usr/ports/www/boost-beast/work/beast-boost-1.81.0/test/beast/core/bind_handler.cpp:367:14: note: in instantiation of function template specialization 'boost::asio::post<boost::asio::io_context, boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>>' requested here  
        net::post(ioc, std::forward<F>(f));  
             ^  
/usr/ports/www/boost-beast/work/beast-boost-1.81.0/test/beast/core/bind_handler.cpp:547:13: note: in instantiation of function template specialization 'boost::beast::bind_handler_test::testHooks<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int>>' requested here  
            testHooks(ioc, bind_front_handler(net::bind_executor(  
            ^  
/usr/local/include/boost/asio/io_context.hpp:1024:8: note: declared private here  
  void execute(BOOST_ASIO_MOVE_ARG(Function) f) const;  
       ^  
/usr/ports/www/boost-beast/work/beast-boost-1.81.0/test/beast/core/bind_handler.cpp:225:47: error: 'execute' is a private member of 'boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>'  
                net::execution::allocator(a)).execute(std::forward<F>(f));  
                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~  
/usr/local/include/boost/asio/detail/work_dispatcher.hpp:136:26: note: in instantiation of function template specialization 'boost::beast::bind_handler_test::test_executor::dispatch<boost::asio::detail::binder0<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int, const char *>>, std::allocator<void>>' requested here  
    work_.get_executor().dispatch(  
                         ^  
/usr/local/include/boost/asio/detail/handler_invoke_helpers.hpp:51:3: note: in instantiation of member function 'boost::asio::detail::work_dispatcher<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int, const char *>, boost::beast::bind_handler_test::test_executor>::operator()' requested here  
  function();  
  ^  
/usr/local/include/boost/asio/impl/io_context.hpp:294:42: note: in instantiation of function template specialization 'boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::work_dispatcher<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int, const char *>, boost::beast::bind_handler_test::test_executor>, boost::asio::detail::work_dispatcher<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int, const char *>, boost::beast::bind_handler_test::test_executor>>' requested here  
      boost_asio_handler_invoke_helpers::invoke(tmp, tmp);  
                                         ^  
/usr/local/include/boost/asio/execution/execute.hpp:208:39: note: in instantiation of function template specialization 'boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>::execute<boost::asio::detail::work_dispatcher<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int, const char *>, boost::beast::bind_handler_test::test_executor>>' requested here  
    return BOOST_ASIO_MOVE_CAST(T)(t).execute(BOOST_ASIO_MOVE_CAST(F)(f));  
                                      ^  
/usr/local/include/boost/asio/async_result.hpp:482:5: note: in instantiation of function template specialization 'boost::asio::detail::initiate_post_with_executor<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>::operator()<boost::beast::detail::bind_front_wrapper<boost::asio::executor_binder<boost::beast::bind_handler_test::test_cb, boost::beast::bind_handler_test::test_executor>, int, const char *>>' requested here  
    BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
    ^  
/usr/local/include/boost/asio/detail/config.hpp:135:37: note: expanded from macro 'BOOST_ASIO_MOVE_CAST'  
# define BOOST_ASIO_MOVE_CAST(type) static_cast<type&&>  
                                    ^  
```  
  
boost-libs-1.80.0  
clang-14  
FreeBSD 13.1
