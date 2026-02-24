# #332 - notify_fork breaks async resolve functionality on linux [Closed]

> Username: umx2000  
> Created at: 2020-03-24 13:07:08 UTC  
> Updated at: 2020-12-30 01:13:34 UTC  
> Closed at: 2020-12-30 01:13:33 UTC  
> Url: https://github.com/boostorg/asio/issues/332  

Hi,  
I see this problem in boost/asio versions 1.70 and 1.72 under debian linux 10.3,  g++ 8.3.0, compiled with --std=c++11.  
  
Steps to reproduce the problem:  
  
- use boost::asio::ip::tcp::resolver's method `async_resolve()` to resolve at least one name (this creates work thread used to perform name resolution in the background)  
- call `notify_fork(boost::asio::io_context::fork_event::fork_prepare)` on the io_context. Currently executing name resolution (if any) finishes and worker thread is joined and destroyed.  
- `fork()` and call `notify_fork(boost::asio::io_context::fork_event::fork_child/parent)` on the io_context. This call doesn't resume name resolution of the pending name queries. Callbacks of the pending and further name queries will never be called.  
- using `async_resolve()` again attempts to recreate background thread but thread exits immediately and no more names are resolved probably because work scheduler was stopped, but not restarted again  
  
Complete program to reproduce the problem:  
```  
#include <iostream>  
#include <boost/asio.hpp>  
  
struct Test1  
{  
    Test1() : m_ctx(), m_sset(m_ctx), m_resolver(m_ctx), m_bFork(false)  
    {  
    }  
  
    void onSignal(const boost::system::error_code &ec, int sig)  
    {  
        if (sig == SIGUSR2)  
        {  
            // fork  
            std::cout << "scheduling fork\n";  
            m_sset.async_wait(std::bind(&Test1::onSignal, this, std::placeholders::_1, std::placeholders::_2));  
            m_bFork = true;  
            m_ctx.stop();  
            return;  
        }  
        if (sig == SIGUSR1)  
        {  
            static uint64_t s_cnt = 0;  
            ++s_cnt;  
            m_resolver.async_resolve("localhost", "80", std::bind(&Test1::onResolve, this, s_cnt, std::placeholders::_1, std::placeholders::_2));  
            std::cout << "resolve " << s_cnt << "\n";  
            m_sset.async_wait(std::bind(&Test1::onSignal, this, std::placeholders::_1, std::placeholders::_2));  
            return;  
        }  
        m_ctx.stop();  
        std::cout << "stopping on signal\n";  
    }  
  
    void onResolve(uint64_t cnt, const boost::system::error_code& ec, boost::asio::ip::tcp::resolver::results_type results)  
    {  
        std::cout << "on resolve " << cnt << "\n";  
        if (ec)  
        {  
            std::cout << " error " << ec.value() << " " << ec.message() << "\n";  
        }  
        else  
        {  
            for (const auto & i : results)  
            {  
                std::cout << i.endpoint().address().to_string() << " " << i.endpoint().port() << "\n";  
            }  
        }  
    }  
  
    void run()  
    {  
        m_sset.add(SIGINT);  
        m_sset.add(SIGTERM);  
        m_sset.add(SIGHUP);  
        m_sset.add(SIGUSR1);  
        m_sset.add(SIGUSR2);  
        m_sset.async_wait(std::bind(&Test1::onSignal, this, std::placeholders::_1, std::placeholders::_2));  
  
        while(1)  
        {  
            m_ctx.run();  
            if (m_bFork)  
            {  
                m_bFork = false;  
                std::cout << "forking\n";  
                m_ctx.notify_fork(boost::asio::io_context::fork_event::fork_prepare);  
                pid_t res = fork();  
                if (res == 0)  
                {  
                    // child  
                    std::cout << "child: stopping\n";  
                    m_ctx.notify_fork(boost::asio::io_context::fork_event::fork_child);  
                }  
                else if (res < 0)  
                {  
                    std::cout << "fork failed\n";  
                }  
                else  
                {  
                    // parent  
                    m_ctx.notify_fork(boost::asio::io_context::fork_event::fork_parent);  
                    std::cout << "parent notified\n";  
                    m_ctx.restart();  
                }  
            }  
            else  
                break;  
        }  
    }  
  
    boost::asio::io_context m_ctx;  
    boost::asio::signal_set m_sset;  
    boost::asio::ip::tcp::resolver m_resolver;  
    bool m_bFork;  
};  
  
int main(int argc, char ** argv)  
{  
    try  
    {  
        Test1 t;  
        t.run();  
        std::cout << "exited\n";  
    }  
    catch (std::exception &e)  
    {  
        std::cerr << "Exception: " << e.what() << "\n";  
        return 1;  
    }  
    return 0;  
}  
```  
After starting this program, send signal USR1 to it, this resolves a name. Then send USR2 so fork is performed. Then try USR1 again - resolve callback won't be called. INT or TERM signals can be used to terminate the test program.  
  
It _seems_ to me that problem is caused by the following code in resolver_service_base.ipp, around line 74. fork_prepare event deletes the thread but child/parent events do not recreate it because of check for thread's existence at the start of the method.  
  
```  
void resolver_service_base::base_notify_fork(  
    execution_context::fork_event fork_ev)  
{  
  if (work_thread_.get())  
  {  
    if (fork_ev == execution_context::fork_prepare)  
    {  
      work_scheduler_->stop();  
      work_thread_->join();  
      work_thread_.reset();  
    }  
    else  
    {  
      work_scheduler_->restart();  
      work_thread_.reset(new boost::asio::detail::thread(  
            work_scheduler_runner(*work_scheduler_)));  
    }  
  }  
}  
```

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:13:32 UTC  
> Url: https://github.com/boostorg/asio/issues/332#issuecomment-752293514  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#753](https://github.com/chriskohlhoff/asio/issues/753).
