# #253 - boost::asio::signal_set crash for boost 1.89.0 [Open]

> Username: nigels-com  
> Created at: 2025-11-04 06:08:58 UTC  
> Updated at: 2025-11-05 03:18:15 UTC  
> Url: https://github.com/boostorg/cobalt/issues/253  

Came across this, possibly "doing it wrong" but possibly a Cobalt bug?  
  
```  
#include <csignal>  
  
#include <boost/cobalt.hpp>  
#include <boost/asio.hpp>  
  
boost::cobalt::task<void> signalWait()  
{  
    auto exec = co_await boost::cobalt::this_coro::executor;  
    boost::asio::signal_set signals(exec, SIGINT);  
    co_await signals.async_wait(boost::cobalt::use_op);  
}  
  
boost::cobalt::main co_main(int argc, char *argv[])  
{  
    co_await boost::cobalt::join(signalWait());  
}  
```  
  
*Expected behaviour* Typing ^C (SIGINT) will be consumed by the signalWait coroutine and the program will exit normally.  
  
*Observed behaviour* ^C (SIGINT) results in segfault  
  
According to gdb:  
  
```  
(gdb) signal 2  
Continuing with signal SIGINT.  
  
Program received signal SIGSEGV, Segmentation fault.  
0x0000555555568743 in boost::asio::cancellation_signal::emit (this=0x7fffffffbc00, type=boost::asio::cancellation_type::total)  
    at /opt/boost-1.89.0/include/boost/asio/cancellation_signal.hpp:99  
99	      handler_->call(type);  
(gdb) where  
#0  0x0000555555568743 in boost::asio::cancellation_signal::emit (this=0x7fffffffbc00, type=boost::asio::cancellation_type::total)  
    at /opt/boost-1.89.0/include/boost/asio/cancellation_signal.hpp:99  
#1  0x0000555555580990 in work::operator() (this=0x7fffffffb7b0, ec=..., sig=2) at /home/nigel-stewart/dev/cobalt/src/main.cpp:45  
#2  0x0000555555581f90 in boost::asio::detail::binder2<boost::cobalt::detail::main_promise::run_main(boost::cobalt::main)::work, boost::system::error_code, int>::operator()(void) (  
    this=0x7fffffffb7b0) at /opt/boost-1.89.0/include/boost/asio/detail/bind_handler.hpp:181  
#3  0x0000555555581edb in boost::asio::detail::handler_work<boost::cobalt::detail::main_promise::run_main(boost::cobalt::main)::work, boost::asio::any_io_executor, void>::complete<boost::asio::detail::binder2<boost::cobalt::detail::main_promise::run_main(boost::cobalt::main)::work, boost::system::error_code, int> >(boost::asio::detail::binder2<boost::cobalt::detail::main_promise::run_main(boost::cobalt::main)::work, boost::system::error_code, int> &, work &) (this=0x7fffffffb7e0, function=..., handler=...)  
    at /opt/boost-1.89.0/include/boost/asio/detail/handler_work.hpp:470  
#4  0x0000555555581ae1 in boost::asio::detail::signal_handler<boost::cobalt::detail::main_promise::run_main(boost::cobalt::main)::work, boost::asio::any_io_executor>::do_complete(void *, boost::asio::detail::operation *, const boost::system::error_code &, std::size_t) (owner=0x5555555c1610, base=0x5555555c1ca0)  
    at /opt/boost-1.89.0/include/boost/asio/detail/signal_handler.hpp:76  
#5  0x0000555555563d46 in boost::asio::detail::scheduler_operation::complete (this=0x5555555c1ca0, owner=0x5555555c1610, ec=..., bytes_transferred=0)  
    at /opt/boost-1.89.0/include/boost/asio/detail/scheduler_operation.hpp:40  
#6  0x0000555555567c4b in boost::asio::detail::scheduler::do_run_one (this=0x5555555c1610, lock=..., this_thread=..., ec=...)  
    at /opt/boost-1.89.0/include/boost/asio/detail/impl/scheduler.ipp:501  
#7  0x00005555555675fa in boost::asio::detail::scheduler::run (this=0x5555555c1610, ec=...) at /opt/boost-1.89.0/include/boost/asio/detail/impl/scheduler.ipp:217  
#8  0x00005555555839bf in boost::asio::io_context::run (this=0x7fffffffbaa0) at /opt/boost-1.89.0/include/boost/asio/impl/io_context.ipp:63  
#9  0x0000555555580c1a in boost::cobalt::detail::main_promise::run_main (mn=...) at /home/nigel-stewart/dev/cobalt/src/main.cpp:61  
#10 0x0000555555569921 in boost::cobalt::detail::main (argc=1, argv=0x7fffffffdcd8) at /home/nigel-stewart/dev/cobalt/include/boost/cobalt/detail/main.hpp:123  
```

---

## Comment 1

> Username: nigels-com  
> Created at: 2025-11-04 06:28:31 UTC  
> Url: https://github.com/boostorg/cobalt/issues/253#issuecomment-3484101069  

Awaiting on a timer seems fine, does not crash.  
  
```  
#include <chrono>  
  
#include <boost/cobalt.hpp>  
#include <boost/asio.hpp>  
  
boost::cobalt::task<void> signalWait()  
{  
    auto exec = co_await boost::cobalt::this_coro::executor;  
    boost::asio::steady_timer timer(exec, std::chrono::milliseconds(1000));  
    co_await timer.async_wait(boost::cobalt::use_op);  
}  
  
boost::cobalt::main co_main(int argc, char *argv[])  
{  
    auto w = signalWait();  
    co_await boost::cobalt::join(w);  
}  
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-11-04 09:27:53 UTC  
> Url: https://github.com/boostorg/cobalt/issues/253#issuecomment-3484863540  

That's a bug I will need to check.  
  
However note that the `main` already capture SIGINT and forwards it as a cancellation to `signalWait` - which is most likely the cause of the bug.

---

## Comment 3

> Username: nigels-com  
> Created at: 2025-11-04 09:52:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/253#issuecomment-3484977625  

Ah, I see.  Thanks for the hint.

---

## Comment 4

> Username: nigels-com  
> Created at: 2025-11-05 03:18:15 UTC  
> Url: https://github.com/boostorg/cobalt/issues/253#issuecomment-3489060557  

Indeed, this works fine, without using `co_main`  
  
```  
#include <cstdlib>  
#include <csignal>  
  
#include <boost/cobalt.hpp>  
#include <boost/asio.hpp>  
  
boost::cobalt::task<void> waitSIGINT()  
{  
    auto exec = co_await boost::cobalt::this_coro::executor;  
    boost::asio::signal_set signals(exec, SIGINT);  
    auto s = co_await signals.async_wait(boost::cobalt::use_op);  
}  
  
int main(int argc, char ** argv)  
{  
    run(waitSIGINT());  
    return EXIT_SUCCESS;  
}  
```
