# #2689 - Valgrind helgrind/DRD report on http_client_async.cpp [Closed]

> Username: philippeVerney  
> Created at: 2023-05-18 13:47:15 UTC  
> Updated at: 2023-10-28 16:31:56 UTC  
> Closed at: 2023-10-28 16:31:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2689  

### Version of Beast  
  
Ubuntu 20.04.6 LTS  
Boost 1.71  
valgrind-3.15.0  
gcc version 9.4.0 (Ubuntu 9.4.0-1ubuntu1~20.04.1)  
  
### Steps necessary to reproduce the problem  
  
Build [libs/beast/example/http/client/async/http_client_async.cpp](https://www.boost.org/doc/libs/1_82_0/libs/beast/example/http/client/async/http_client_async.cpp) using this command line `g++ http_client_async.cpp -lpthread`  
Run `valgrind --tool=helgrind ./a.out www.example.com 80 /` and/or `valgrind --tool=drd ./a.out www.example.com 80 /`  
  
### All relevant compiler information  
  
Valgrind helgrind reports 1 error from 1 context  
  
```  
==209== Helgrind, a thread error detector  
==209== Copyright (C) 2007-2017, and GNU GPL'd, by OpenWorks LLP et al.  
==209== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info  
==209== Command: ./a.out www.example.com 80 /  
==209==  
==209== ---Thread-Announcement------------------------------------------  
==209==  
==209== Thread #1 is the program's root thread  
==209==  
==209== ----------------------------------------------------------------  
==209==  
==209== Thread #1: pthread_cond_{signal,broadcast}: dubious: associated lock is not held by any thread  
==209==    at 0x48405D6: ??? (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_helgrind-amd64-linux.so)  
==209==    by 0x1287EF: bool boost::asio::detail::posix_event::maybe_unlock_and_signal_one<boost::asio::detail::conditionally_enabled_mutex::scoped_lock>(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x118711: boost::asio::detail::conditionally_enabled_event::maybe_unlock_and_signal_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x11C14C: boost::asio::detail::scheduler::wake_one_thread_and_unlock(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x11BB78: boost::asio::detail::scheduler::post_immediate_completion(boost::asio::detail::scheduler_operation*, bool) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x12010D: boost::asio::detail::resolver_service_base::start_resolve_op(boost::asio::detail::resolve_op*) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x142E5E: void boost::asio::detail::resolver_service<boost::asio::ip::tcp>::async_resolve<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, boost::asio::detail::io_object_executor<boost::asio::executor> >(std::shared_ptr<void>&, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp> const&, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&, boost::asio::detail::io_object_executor<boost::asio::executor> const&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x13DF08: void boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve::operator()<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp> >(boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&&, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp> const&) const (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x138447: void boost::asio::async_result<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::initiate<boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp>&>(boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve&&, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&&, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*&&, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp>&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x134E6B: std::enable_if<boost::asio::detail::async_result_has_initiate_memfn<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::value, boost::asio::async_result<std::decay<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >::type, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::return_type>::type boost::asio::async_initiate<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp>&>(boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve&&, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*&&, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp>&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x1303E4: boost::asio::async_result<std::decay<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >::type, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::return_type boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::async_resolve<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >(std::experimental::fundamentals_v1::basic_string_view<char, std::char_traits<char> >, std::experimental::fundamentals_v1::basic_string_view<char, std::char_traits<char> >, boost::asio::ip::resolver_base::flags, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==    by 0x12B5F1: boost::asio::async_result<std::decay<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >::type, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::return_type boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::async_resolve<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >(std::experimental::fundamentals_v1::basic_string_view<char, std::char_traits<char> >, std::experimental::fundamentals_v1::basic_string_view<char, std::char_traits<char> >, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==209==  
HTTP/1.1 200 OK  
Age: 551620  
Cache-Control: max-age=604800  
Content-Type: text/html; charset=UTF-8  
Date: Thu, 18 May 2023 13:38:44 GMT  
Etag: "3147526947+gzip+ident"  
Expires: Thu, 25 May 2023 13:38:44 GMT  
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT  
Server: ECS (bsa/EB24)  
Vary: Accept-Encoding  
X-Cache: HIT  
Content-Length: 1256  
  
<!doctype html>  
<html>  
<head>  
    <title>Example Domain</title>  
  
    <meta charset="utf-8" />  
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />  
    <meta name="viewport" content="width=device-width, initial-scale=1" />  
    <style type="text/css">  
    body {  
        background-color: #f0f0f2;  
        margin: 0;  
        padding: 0;  
        font-family: -apple-system, system-ui, BlinkMacSystemFont, "Segoe UI", "Open Sans", "Helvetica Neue", Helvetica, Arial, sans-serif;  
  
    }  
    div {  
        width: 600px;  
        margin: 5em auto;  
        padding: 2em;  
        background-color: #fdfdff;  
        border-radius: 0.5em;  
        box-shadow: 2px 3px 7px 2px rgba(0,0,0,0.02);  
    }  
    a:link, a:visited {  
        color: #38488f;  
        text-decoration: none;  
    }  
    @media (max-width: 700px) {  
        div {  
            margin: 0 auto;  
            width: auto;  
        }  
    }  
    </style>  
</head>  
  
<body>  
<div>  
    <h1>Example Domain</h1>  
    <p>This domain is for use in illustrative examples in documents. You may use this  
    domain in literature without prior coordination or asking for permission.</p>  
    <p><a href="https://www.iana.org/domains/example">More information...</a></p>  
</div>  
</body>  
</html>  
  
==209==  
==209== Use --history-level=approx or =none to gain increased speed, at  
==209== the cost of reduced accuracy of conflicting-access information  
==209== For lists of detected and suppressed errors, rerun with: -s  
==209== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 224 from 19)  
```  
  
valgrid drd reports similarly 1 error from 1 context  
  
```  
==211== drd, a thread error detector  
==211== Copyright (C) 2006-2017, and GNU GPL'd, by Bart Van Assche.  
==211== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info  
==211== Command: ./a.out www.example.com 80 /  
==211==  
==211== Probably a race condition: condition variable 0x4de3850 has been signaled but the associated mutex 0x4de3818 is not locked by the signalling thread.  
==211==    at 0x484839B: pthread_cond_signal@* (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_drd-amd64-linux.so)  
==211==    by 0x1287EF: bool boost::asio::detail::posix_event::maybe_unlock_and_signal_one<boost::asio::detail::conditionally_enabled_mutex::scoped_lock>(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x118711: boost::asio::detail::conditionally_enabled_event::maybe_unlock_and_signal_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x11C14C: boost::asio::detail::scheduler::wake_one_thread_and_unlock(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x11BB78: boost::asio::detail::scheduler::post_immediate_completion(boost::asio::detail::scheduler_operation*, bool) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x12010D: boost::asio::detail::resolver_service_base::start_resolve_op(boost::asio::detail::resolve_op*) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x142E5E: void boost::asio::detail::resolver_service<boost::asio::ip::tcp>::async_resolve<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, boost::asio::detail::io_object_executor<boost::asio::executor> >(std::shared_ptr<void>&, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp> const&, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&, boost::asio::detail::io_object_executor<boost::asio::executor> const&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x13DF08: void boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve::operator()<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp> >(boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&&, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp> const&) const (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x138447: void boost::asio::async_result<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::initiate<boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp>&>(boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve&&, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&&, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*&&, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp>&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x134E6B: std::enable_if<boost::asio::detail::async_result_has_initiate_memfn<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::value, boost::asio::async_result<std::decay<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >::type, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::return_type>::type boost::asio::async_initiate<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp>&>(boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::initiate_async_resolve&&, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&, boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>*&&, boost::asio::ip::basic_resolver_query<boost::asio::ip::tcp>&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x1303E4: boost::asio::async_result<std::decay<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >::type, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::return_type boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::async_resolve<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >(std::experimental::fundamentals_v1::basic_string_view<char, std::char_traits<char> >, std::experimental::fundamentals_v1::basic_string_view<char, std::char_traits<char> >, boost::asio::ip::resolver_base::flags, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x12B5F1: boost::asio::async_result<std::decay<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >::type, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>::return_type boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::async_resolve<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> > >(std::experimental::fundamentals_v1::basic_string_view<char, std::char_traits<char> >, std::experimental::fundamentals_v1::basic_string_view<char, std::char_traits<char> >, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>), std::shared_ptr<session> >&&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211== cond 0x4de3850 was first observed at:  
==211==    at 0x4846F6B: pthread_cond_init@* (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_drd-amd64-linux.so)  
==211==    by 0x11851C: boost::asio::detail::posix_event::posix_event() (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x118604: boost::asio::detail::conditionally_enabled_event::conditionally_enabled_event() (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x11B1FF: boost::asio::detail::scheduler::scheduler(boost::asio::execution_context&, int, bool) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x11FD5E: boost::asio::detail::resolver_service_base::resolver_service_base(boost::asio::execution_context&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x141C78: boost::asio::detail::resolver_service<boost::asio::ip::tcp>::resolver_service(boost::asio::execution_context&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x13CBBE: boost::asio::execution_context::service* boost::asio::detail::service_registry::create<boost::asio::detail::resolver_service<boost::asio::ip::tcp>, boost::asio::execution_context>(void*) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x117CEA: boost::asio::detail::service_registry::do_use_service(boost::asio::execution_context::service::key const&, boost::asio::execution_context::service* (*)(void*), void*) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x1375E9: boost::asio::detail::resolver_service<boost::asio::ip::tcp>& boost::asio::detail::service_registry::use_service<boost::asio::detail::resolver_service<boost::asio::ip::tcp> >() (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x13318E: boost::asio::detail::resolver_service<boost::asio::ip::tcp>& boost::asio::use_service<boost::asio::detail::resolver_service<boost::asio::ip::tcp> >(boost::asio::execution_context&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x12ECC8: boost::asio::detail::io_object_impl<boost::asio::detail::resolver_service<boost::asio::ip::tcp>, boost::asio::executor>::io_object_impl(boost::asio::executor const&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x12AD4F: boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::basic_resolver(boost::asio::executor const&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211== mutex 0x4de3818 was first observed at:  
==211==    at 0x4844C36: pthread_mutex_init (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_drd-amd64-linux.so)  
==211==    by 0x1179C8: boost::asio::detail::posix_mutex::posix_mutex() (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x1183A7: boost::asio::detail::conditionally_enabled_mutex::conditionally_enabled_mutex(bool) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x11B1EC: boost::asio::detail::scheduler::scheduler(boost::asio::execution_context&, int, bool) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x11FD5E: boost::asio::detail::resolver_service_base::resolver_service_base(boost::asio::execution_context&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x141C78: boost::asio::detail::resolver_service<boost::asio::ip::tcp>::resolver_service(boost::asio::execution_context&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x13CBBE: boost::asio::execution_context::service* boost::asio::detail::service_registry::create<boost::asio::detail::resolver_service<boost::asio::ip::tcp>, boost::asio::execution_context>(void*) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x117CEA: boost::asio::detail::service_registry::do_use_service(boost::asio::execution_context::service::key const&, boost::asio::execution_context::service* (*)(void*), void*) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x1375E9: boost::asio::detail::resolver_service<boost::asio::ip::tcp>& boost::asio::detail::service_registry::use_service<boost::asio::detail::resolver_service<boost::asio::ip::tcp> >() (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x13318E: boost::asio::detail::resolver_service<boost::asio::ip::tcp>& boost::asio::use_service<boost::asio::detail::resolver_service<boost::asio::ip::tcp> >(boost::asio::execution_context&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x12ECC8: boost::asio::detail::io_object_impl<boost::asio::detail::resolver_service<boost::asio::ip::tcp>, boost::asio::executor>::io_object_impl(boost::asio::executor const&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==    by 0x12AD4F: boost::asio::ip::basic_resolver<boost::asio::ip::tcp, boost::asio::executor>::basic_resolver(boost::asio::executor const&) (in /home/philippe/dev/fesapiEnv/http_client_async-build/a.out)  
==211==  
HTTP/1.1 200 OK  
Age: 551764  
Cache-Control: max-age=604800  
Content-Type: text/html; charset=UTF-8  
Date: Thu, 18 May 2023 13:41:08 GMT  
Etag: "3147526947+gzip+ident"  
Expires: Thu, 25 May 2023 13:41:08 GMT  
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT  
Server: ECS (bsa/EB24)  
Vary: Accept-Encoding  
X-Cache: HIT  
Content-Length: 1256  
  
<!doctype html>  
<html>  
<head>  
    <title>Example Domain</title>  
  
    <meta charset="utf-8" />  
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />  
    <meta name="viewport" content="width=device-width, initial-scale=1" />  
    <style type="text/css">  
    body {  
        background-color: #f0f0f2;  
        margin: 0;  
        padding: 0;  
        font-family: -apple-system, system-ui, BlinkMacSystemFont, "Segoe UI", "Open Sans", "Helvetica Neue", Helvetica, Arial, sans-serif;  
  
    }  
    div {  
        width: 600px;  
        margin: 5em auto;  
        padding: 2em;  
        background-color: #fdfdff;  
        border-radius: 0.5em;  
        box-shadow: 2px 3px 7px 2px rgba(0,0,0,0.02);  
    }  
    a:link, a:visited {  
        color: #38488f;  
        text-decoration: none;  
    }  
    @media (max-width: 700px) {  
        div {  
            margin: 0 auto;  
            width: auto;  
        }  
    }  
    </style>  
</head>  
  
<body>  
<div>  
    <h1>Example Domain</h1>  
    <p>This domain is for use in illustrative examples in documents. You may use this  
    domain in literature without prior coordination or asking for permission.</p>  
    <p><a href="https://www.iana.org/domains/example">More information...</a></p>  
</div>  
</body>  
</html>  
  
==211==  
==211== For lists of detected and suppressed errors, rerun with: -s  
==211== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 12 from 8)  
```  
  
I have found this topic on the web which seems to indicate that it is _just inefficient though, not a logic error_ : https://stackoverflow.com/questions/68787265/helgrind-reports-synchronization-errors-in-simple-boostasiothread-pool-progr  
  
One of my BEAST application user faces a memory error. He ran valgrind and found a very similar report. I thought it was the cause of his crash but seeing the same report in the documentation BEAST example makes now me thinking that this report cannot cause any crash.  
Should I ignore those valgrind reports?

---

## Comment 1

> Username: ashtum  
> Created at: 2023-08-19 05:45:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2689#issuecomment-1684837831  

@philippeVerney As you might know Asio spawns a background threads for resolve operation (because there is no asynchronous version of it in linux), this error seems to be a false positive in Asio's scheduler. This is probably a relate issue: https://github.com/eProsima/Fast-DDS/issues/133
