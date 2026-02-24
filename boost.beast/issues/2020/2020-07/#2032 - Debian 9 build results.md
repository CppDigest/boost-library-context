# #2032 - Debian 9 build results [Closed]

> Username: sdarwin  
> Created at: 2020-07-22 14:42:29 UTC  
> Updated at: 2020-07-24 12:06:55 UTC  
> Closed at: 2020-07-24 12:06:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2032  

### Version of Beast  
  
299  
### Steps necessary to reproduce the problem  
  
(from beast/pull/2028)  
  
docker build -f .dockers/debian-9/Dockerfile .  
  
### build output:  
```  
...failed updating 1 target...  
...updated 1816 targets...  
  
In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/6.3.0/../../../../include/c++/6.3.0/functional:55:  
/usr/bin/../lib/gcc/x86_64-linux-gnu/6.3.0/../../../../include/c++/6.3.0/tuple:483:68: error: no member named 'value' in 'std::__and_<std::is_constructible<boost:     :asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boos     t::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::exec     ution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstandi     ng_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boos     t::asio::execution::detail::relationship::continuation_t<0> > > >, std::tuple<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_e     xecutor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::pref     er_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t     <0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::ex     ecution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > > &&> >'  
      return __and_<is_constructible<_Elements, _UElements&&>...>::value;  
  
  
2 errors generated.  
  
  "clang++" -c -x c++ -std=c++14 -fvisibility-inlines-hidden -Wno-c99-extensions -fPIC -m64 -pthread -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB     =1 -DBOOST_ASIO_DISABLE_BOOST_ARRAY=1 -DBOOST_ASIO_DISABLE_BOOST_BIND=1 -DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1 -DBOOST_ASIO_DISABLE_BOOST_REGEX=1 -DBOOST_ASIO_NO_     DEPRECATED=1 -DBOOST_ASIO_SEPARATE_COMPILATION -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1 -DNDE     BUG -D_GNU_SOURCE=1 -D_XOPEN_SOURCE=600 -I"." -I"libs/beast"  -o "bin.v2/libs/beast/example/websocket/server/sync/clang-linux-3.8.1/release/cxxstd-14-iso/threadin     g-multi/visibility-hidden/websocket_server_sync.o" "libs/beast/example/websocket/server/sync/websocket_server_sync.cpp"  
  
...failed clang-linux.compile.c++.without-pch bin.v2/libs/beast/example/websocket/server/sync/clang-linux-3.8.1/release/cxxstd-14-iso/threading-multi/visibility-h     idden/websocket_server_sync.o...  
clang-linux.compile.c++.without-pch bin.v2/libs/beast/example/websocket/server/stackless/clang-linux-3.8.1/release/cxxstd-14-iso/threading-multi/visibility-hidden     /websocket_server_stackless.o  
link-o bin.v2/libs/beast/example/websocket/server/stackless/clang-linux-3.8.1/release/cxxstd-14-iso/threading-multi/visibility-hidden/websocket-server-stackless  
clang-linux.compile.c++.without-pch bin.v2/libs/beast/example/websocket/server/coro/clang-linux-3.8.1/release/cxxstd-14-iso/threadapi-pthread/threading-multi/visi     bility-hidden/websocket_server_coro.o  
In file included from libs/beast/example/websocket/server/coro/websocket_server_coro.cpp:18:  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-23 08:31:56 UTC  
> Updated at: 2020-07-23 08:33:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2032#issuecomment-662884530  

The code is trying to deduce whether a socket is move-constructible. But it looks like either an error in libstdc++ or   
the compiler is preventing compilation.  
  
Who's error this is is an interesting question: Compiler, standard library or Asio.   
  
@chriskohlhoff Do you have a view?  
  
This is a copy of the line of code originating the error:  
```  
            // Launch the session, transferring ownership of the socket  
            std::thread{std::bind(  
                &do_session,  
                std::move(socket))}.detach();  
```

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-07-23 08:47:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2032#issuecomment-662890964  

Substituting boost::bind in place of std::bind is I think a little more revealing:  
  
```  
./boost/bind/bind.hpp:112:7: note: copy constructor of 'value<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > >' is implicitly deleted because field 't_' has a deleted copy constructor  
    T t_;  
      ^  
./boost/asio/basic_stream_socket.hpp:978:3: note: 'basic_stream_socket' has been explicitly marked deleted here  
  basic_stream_socket(const basic_stream_socket&) BOOST_ASIO_DELETED;  
  ^  
1 warning and 3 errors generated  
```  
  
It's expected that sockets are not copyable. To me it seems that either:  
* asio's config.hpp is not detecting the presence of r-value references in this configuration of compiler & library, or  
* there is a fault in this compiler's library.  
  
I suspect the first is more likely.  
  
Command line is:  
```  
  "clang++" -c -x c++ -std=c++14 -fvisibility-inlines-hidden -Wno-c99-extensions -fPIC -m64 -pthread -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_DISABLE_BOOST_ARRAY=1 -DBOOST_ASIO_DISABLE_BOOST_BIND=1 -DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1 -DBOOST_ASIO_DISABLE_BOOST_REGEX=1 -DBOOST_ASIO_NO_DEPRECATED=1 -DBOOST_ASIO_NO_TS_EXECUTORS -DBOOST_ASIO_SEPARATE_COMPILATION -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1 -DNDEBUG -D_GNU_SOURCE=1 -D_XOPEN_SOURCE=600 -I"." -I"libs/beast"  -o "bin.v2/libs/beast/example/websocket/server/sync/clang-linux-3.8.1/release/asio.mode-nots/cxxstd-14-iso/threading-multi/visibility-hidden/websocket_server_sync.o" "libs/beast/example/websocket/server/sync/websocket_server_sync.cpp"  
```  
  
Compiler is:  
  
```  
clang version 3.8.1-24 (tags/RELEASE_381/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-07-23 08:58:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2032#issuecomment-662895484  

I was wrong. Of course Chris' code is more stable than the foundation of the language itself.  
  
https://godbolt.org/z/o19ffx  
  
Solution: don't use this compiler. It has a faulty standard library.

---

## Comment 4

> Username: chriskohlhoff  
> Created at: 2020-07-23 09:19:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2032#issuecomment-662905085  

You can also pass variadic arguments directly to the std thread constructor without std bind.  
  
On Thu, Jul 23, 2020, at 6:32 PM, Richard Hodges wrote:  
>   
>   
> The code is trying to deduce whether a socket is move-constructible.   
> But it looks like either an error in libstdc++ or  
> the compiler is preventing compilation.  
>   
> Who's error this is is an interesting question: Compiler, standard   
> library or Asio.  
>   
> @chriskohlhoff <https://github.com/chriskohlhoff> Do you have a view?  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/beast/issues/2032#issuecomment-662884530>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AADQ5SRVKJEPTZYZDGWDFE3R47YQ3ANCNFSM4PEY6QEA>.  
>
