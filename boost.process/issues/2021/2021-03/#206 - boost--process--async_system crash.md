# #206 - boost::process::async_system crash [Open]

> Username: jade2k11598  
> Created at: 2021-03-27 17:38:43 UTC  
> Updated at: 2021-04-01 16:02:20 UTC  
> Url: https://github.com/boostorg/process/issues/206  

I have a code that needs to make a series of `boost::process::async_system` calls that periodically crashes mid-way or gets hung at shutdown.    
This was built with boost 1.72 on ubuntu 18.04, g++ (Ubuntu 5.5.0-12ubuntu1) 5.5.0 20171010  
  
I was able to simplify this code, where it just triggers the command line `ls -l`.  I set up a pool of `boost::asio` threads to which the `boost::process::async_system` gets posted to.  I don't think that this pool of threads managed by`boost::asio` is the problem since I've used it with boost networks for some time now with no problems, but I'll attached that code as well.  
  
Below is the simplified code.  It serially calls `boost::process::async_system`, that is, as soon as the first async_system returns via callback function, I printout the results of `ls -l` and I post another `boost::process::async_system` using the same command.  What you pass to main dictates, how many of this series of `async_system` calls to make.  If you pass it zero, it will only call it once.  This `IOService` is the pool of threads that runs the `boost::asio` (see attachment for that); in this case it ran a pool of 4 threads.  For logging I just use a thread-safe log (`spdlog`) of which I've used often without problems.  You can replace it with some other thread-safe logs (didn't want to drag in `spdlog` into the files I attached).  I put some flushes at various places just to get an idea how far it got before the crash (the flushes should have no impact since it still crashed or hung without it):  
```  
class A: public std::enable_shared_from_this<A>  
{  
public:  
    A(boost::asio::io_context &ioc,  
      std::promise<void> &prom, size_t num):  
        m_log(util::registerLogName(util::getLogger(), "A")),  
        m_ioc(ioc), m_prom(prom), m_num(num), m_ct(0) {}  
  
    void start()  
    {  
        issueCmd();  
    }  
private:  
    void issueCmd();  
    void onReturn(std::shared_ptr<boost::process::ipstream> pStr,  
                  const boost::system::error_code &ec, int rc);  
    spdlogger_ptr m_log;  
    boost::asio::io_context &m_ioc;  
    std::promise<void> &m_prom;  
    size_t m_num;  
    size_t m_ct;  
};  
  
void A::issueCmd()  
{  
    m_log->debug("Entered A::issueCmd()");  
    auto pipeStr = std::make_shared<boost::process::ipstream>();  
    boost::process::async_system(  
        m_ioc,  
        boost::bind(&A::onReturn,  
                    shared_from_this(),  
                    pipeStr, // grab copy of this shared_ptr  
                    boost::asio::placeholders::error,  
                    boost::asio::placeholders::bytes_transferred),  
        "ls -l",  
        boost::process::std_out > *pipeStr);  
    m_log->debug("Exited  A::issueCmd()");  
}  
  
void A::onReturn(std::shared_ptr<boost::process::ipstream> pStr,  
              const boost::system::error_code &ec, int rc)  
{  
    m_log->debug("A::onReturn triggered");  
    if (rc != 0 || ec)  
    {  
        m_log->error("Cmd failed: {} {}",  
                     rc, ((ec)?ec.message():""));  
        // false alarm  
        if (rc == -1)  
            return;  
    }  
    m_log->flush();  
  
    std::string results;  
    while (*pStr)  
    {  
        std::string line;  
        std::getline(*pStr, line);  
        results.append(line);  
    }  
    pStr->close();  
    m_log->info("ls -l results:\n{}", results);  
  
    ++m_ct;  
    m_log->debug("ct={}", m_ct);  
    m_log->flush();  
    if (m_ct <= m_num)  
    {  
        issueCmd();  
    }  
    else  
    {  
        m_prom.set_value();  
    }  
}  
  
int main(int argc, char **argv)  
{  
    if (argc != 2)  
    {  
        usage(argv[0]);  
        return 1;  
    }  
  
    int num = 0;  
    if (!sscanf(argv[1], "%d", &num))  
    {  
        std::cerr << "failed to read first arg" << std::endl;  
        return 2;  
    }  
  
    util::LogSpecs::execName = std::string(argv[0]);  
    auto &log = util::getLogger();  
  
    auto ioSvcPtr =  
        std::make_shared<IOService>(4);  
    ioSvcPtr->run();  
  
    std::promise<void> p;  
    auto fut = p.get_future();  
    auto aPtr = std::make_shared<A>(ioSvcPtr->get(), p, num);  
  
    aPtr->start();  
  
    // blocks  
    fut.get();  
    log->info("Exiting ...");  
    log->flush();  
    return 0;  
}  
```  
So when you run this with just one `async_system` call (that is pass the program with a 0 argument), it'll run fine and exit.  But anything beyond a single `async_system` call will either cause a hanging or crash.    
  
When I pass the program a 5 (which should trigger 6 `async_system` calls), it `Segmentation fault` and this was the call stack from the core file it produced:  
```  
#0  tcache_get (tc_idx=1) at malloc.c:2952  
2952	malloc.c: No such file or directory.  
[Current thread is 1 (Thread 0x7f10f806b700 (LWP 14339))]  
#0  tcache_get (tc_idx=1) at malloc.c:2952  
#1  __GI___libc_malloc (bytes=31) at malloc.c:3060  
#2  0x00007f10fa4c8298 in operator new(unsigned long) () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#3  0x0000560b2fa1415b in std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_construct<char*> (this=0x7f10f80697b0,   
...  
    at /home/jade/thirdParty/boost-alert-1.72.0-release-linux-x86_64/include/boost/process/async_system.hpp:92  
#31 0x0000560b2fa40cb2 in std::_Function_handler<void (int, std::error_code const&), std::function<void (int, std::error_code const&)> boost::process::detail::async_system_handler<boost::_bi::bind_t<void, boost::_mfi::mf3<void, A, std::shared_ptr<boost::process::basic_ipstream<char, std::char_traits<char> > >, boost::system::error_code const&, int>, boost::_bi::list4<boost::_bi::value<std::shared_ptr<A> >, boost::_bi::value<std::shared_ptr<boost::process::basic_ipstream<char, std::char_traits<char> > > >, boost::arg<1> (*)(), boost::arg<2> (*)()> > >::on_exit_handler<boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::io_context_ref, boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<boost::asio::io_context&, char const (&) [6], boost::process::detail::posix::pipe_out<1, -1>&, boost::process::detail::async_system_handler<boost::_bi::bind_t<void, boost::_mfi::mf3<void, A, std::shared_ptr<boost::process::basic_ipstream<char, std::char_traits<char> > >, boost::system::error_code const&, int>, boost::_bi::list4<boost::_bi::value<std::shared_ptr<A> >, boost::_bi::value<std::shared_ptr<boost::process::basic_ipstream<char, std::char_traits<char> > > >, boost::arg<1> (*)(), boost::arg<2> (*)()> > >&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > > >(boost::process::detail::posix::executor<boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::posix::io_context_ref, boost::process::detail::posix::exe_cmd_init<char> >, boost::fusion::filter_view<boost::fusion::tuple<boost::asio::io_context&, char const (&) [6], boost::process::detail::posix::pipe_out<1, -1>&, boost::process::detail::async_system_handler<boost::_bi::bind_t<void, boost::_mfi::mf3<void, A, std::shared_ptr<boost::process::basic_ipstream<char, std::char_traits<char> > >, boost::system::error_code const&, int>, boost::_bi::list4<boost::_bi::value<std::shared_ptr<A> >, boost::_bi::value<std::shared_ptr<boost::process::basic_ipstream<char, std::char_traits<char> > > >, boost::arg<1> (*)(), boost::arg<2> (*)()> > >&> const, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >&)::{lambda(int, std::error_code const&)#2}>::_M_invoke(std::_Any_data const&, int&&, std::error_code const&) (__functor=..., __args#0=@0x7f10f806a574: 0, __args#1=...)  
    at /usr/include/c++/5/functional:1871  
#32 0x0000560b2fa32308 in std::function<void (int, std::error_code const&)>::operator()(int, std::error_code const&) const (this=0x7f10ec002870, __args#0=0, __args#1=...) at /usr/include/c++/5/functional:2267  
...  
```  
It's a long stack which I also attached.  From what I can gather from the log (which is also attached), it appears to be in the process of posting the 4th `async_system` call.  
  
It doesn't always crash at the same place.  Other times it crashes as it's going down and gives this shorter stack trace:  
```  
#0  tcache_thread_shutdown () at malloc.c:2978  
#1  arena_thread_freeres () at arena.c:950  
#2  0x00007f9a58ed55e2 in __libc_thread_freeres () at thread-freeres.c:29  
#3  0x00007f9a59131700 in start_thread (arg=0x7f9a57998700) at pthread_create.c:476  
#4  0x00007f9a58e5a71f in clone () at ../sysdeps/unix/sysv/linux/x86_64/clone.S:95  
```  
  
It either hangs or crashes as long as I have more than one `async_system` call and the stack trace always involving `tcache`.  
  
So I had to attach the sample source code as *.cpp.txt and *.hpp.txt since it would not attach for hpp/cpp files.  The log and the backtrace are also attached.  
  
[asyncSysTest-2021-03-27-13.17.07.153362.log.txt](https://github.com/boostorg/process/files/6216084/asyncSysTest-2021-03-27-13.17.07.153362.log.txt)  
[backtrace.txt](https://github.com/boostorg/process/files/6216085/backtrace.txt)  
[async_system_test.cpp.txt](https://github.com/boostorg/process/files/6216086/async_system_test.cpp.txt)  
[IOService.hpp.txt](https://github.com/boostorg/process/files/6216087/IOService.hpp.txt)

---

## Comment 1

> Username: jade2k11598  
> Created at: 2021-03-27 18:10:40 UTC  
> Url: https://github.com/boostorg/process/issues/206#issuecomment-808774968  

One more observation.  I have noticed that _sometimes_ the callback function gets triggered prematurely, returning a return code of -1 with `ec.message()` of " No child processes" (you should see this in the log I attached above), even though the task is not finished.  In the callback function `A::onReturn`, I actually ignore this (label it as false alarm) but have wondered why this intermittently happens and sometimes it happens without incident (crash or hang).  I wouldn't be surprised if this is related to corruption observed above (e.g. not every corruption leads to a crash/hang).

---

## Comment 2

> Username: jade2k11598  
> Created at: 2021-04-01 15:57:31 UTC  
> Updated at: 2021-04-01 16:02:20 UTC  
> Url: https://github.com/boostorg/process/issues/206#issuecomment-812005203  

BTW I also ran valgrind on this sample code above and it indicates some corruption (e.g. invalid writes) triggered by sigchld_service.hpp.  I've attached the valgrind log.  
[val.txt](https://github.com/boostorg/process/files/6244842/val.txt)
