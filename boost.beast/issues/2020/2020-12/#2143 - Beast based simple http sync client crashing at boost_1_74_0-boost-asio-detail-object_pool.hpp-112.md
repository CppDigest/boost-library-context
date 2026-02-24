# #2143 - Beast based simple http sync client crashing at boost_1_74_0/boost/asio/detail/object_pool.hpp:112 [Closed]

> Username: chiragjain1293  
> Created at: 2020-12-24 10:34:03 UTC  
> Updated at: 2021-01-02 17:56:49 UTC  
> Closed at: 2021-01-02 16:58:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2143  

Boost version :1.74  
  
Beast based simple http sync client crashing at boost_1_74_0/boost/asio/detail/object_pool.hpp:112 while integrating with application.  
  
  
#0  alloc<bool> (arg=<optimized out>, this=0x7f275e73e480)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/detail/object_pool.hpp:112  
112           free_list_ = object_pool_access::next(free_list_);  
warning: File "/usr/local/lib64/libstdc++.so.6.0.21-gdb.py" auto-loading has been declined by your `auto-load safe-path' set to "$debugdir:$datadir/auto-load:/usr/bin/mono-gdb.py".  
To enable execution of this file add  
        add-auto-load-safe-path /usr/local/lib64/libstdc++.so.6.0.21-gdb.py  
line to your configuration file "/root/.gdbinit".  
To completely disable this security protection add  
        set auto-load safe-path /  
line to your configuration file "/root/.gdbinit".  
For more information about this security protection see the  
"Auto-loading safe path" section in the GDB manual.  E.g., run from the shell:  
        info "(gdb)Auto-loading safe path"  
Missing separate debuginfos, use: debuginfo-install glibc-2.17-307.el7.1.x86_64 lksctp-tools-1.0.17-2.el7.x86_64  
(gdb) bt full  
#0  alloc<bool> (arg=<optimized out>, this=0x7f275e73e480)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/detail/object_pool.hpp:112  
        o = 0x9  
#1  allocate_descriptor_state (this=0x7f275e73e3c0)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/detail/impl/epoll_reactor.ipp:629  
        descriptors_lock = <optimized out>  
#2  register_descriptor (descriptor_data=@0x7f2748001540: 0x0, descriptor=9, this=0x7f275e73e3c0)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/detail/impl/epoll_reactor.ipp:154  
        ev = {events = 1584653008, data = {ptr = 0xbb0dda2931007f27, fd = 822116135, u32 = 822116135,  
            u64 = 13478669130240392999}}  
        result = <optimized out>  
#3  do_open (ec=..., protocol=6, type=1, af=2, impl=..., this=<optimized out>)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/detail/impl/reactive_socket_service_base.ipp:186  
        sock = {<boost::asio::detail::noncopyable> = {<No data fields>}, socket_ = 9}  
#4  open (ec=..., protocol=..., impl=..., this=<optimized out>)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/detail/reactive_socket_service.hpp:128  
No locals.  
#5  connect (ec=..., peer_endpoint=..., this=0x7f2748001530)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/basic_socket.hpp:890  
No locals.  
#6  boost::asio::connect<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >, boost::asio::ip::basic_resolver_iterator<boost::asio::ip::tcp>, boost::asio::detail::default_connect_condition> (s=..., begin=..., end=..., connect_condition=..., ec=...)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/impl/connect.hpp:242  
        iter = <optimized out>  
#7  0x0000000000acd695 in boost::asio::connect<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::exe---Type <return> to continue, or q <return> to quit---  
cution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp> > (s=..., endpoints=..., ec=...)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/impl/connect.hpp:126  
No locals.  
#8  0x0000000000ac6f9d in connect<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp> > (endpoints=..., s=...) at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/asio/impl/connect.hpp:113  
        ec = {val_ = 1, failed_ = false, cat_ = 0x1000158c6}  
#9  connect<boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>, void> (endpoints=..., this=0x7f2748000d60)  
    at /tmp/abc_boost_install_tmp/boost_1_74_0/boost/beast/core/basic_stream.hpp:597

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-12-24 11:34:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2143#issuecomment-750856670  

Hi, thanks for reporting.  
  
When you write "while integrating with application", does this mean that the example application has been modified?  
  
If so, would you be in a position to share a github repository containing the new code so that I can replicate the error and help you debug?

---

## Comment 2

> Username: chiragjain1293  
> Created at: 2020-12-24 11:51:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2143#issuecomment-750860792  

No Example code is not modified but was used in application where crash occured on connect. No github repository is available to share due to usage in proprietary code.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-12-24 15:18:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2143#issuecomment-750909179  

> No Example code is not modified but was used in application where crash occured on connect.  
  
This must mean that it has been modified, unless the proprietary application launches the example executable as a child process, no?  
  
Would you be able to provide a new non-proprietary program on github that exhibits the same problem, so that I can investigate?  
  
At the moment I don't have much to go on. Our examples are checked on multiple platforms and toolchains prior to release. Any clues would be helpful.

---

## Comment 4

> Username: chiragjain1293  
> Created at: 2021-01-02 16:58:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2143#issuecomment-753499423  

It was due to multiple version of boost present on system. Code was using library of boost version 1.69. Sorry for Troubles

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-01-02 17:56:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2143#issuecomment-753508588  

No worries. Thanks for letting us know.
