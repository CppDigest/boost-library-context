# #425 - Within win_iocp_io_context::shutdown() deadlock on GetQueuedCompletionStatus [Open]

> Username: SunriseChen  
> Created at: 2023-12-12 03:57:12 UTC  
> Updated at: 2023-12-12 04:03:36 UTC  
> Url: https://github.com/boostorg/asio/issues/425  

The following code causes GetQueuedCompletionStatus(...) within win_iocp_io_context::shutdown() to deadlock.  
```c++  
#include <gtest/gtest.h>  
#include <boost/asio.hpp>  
  
using namespace std;  
using namespace boost::asio;  
  
class AsioTimer  
{  
public:  
    explicit AsioTimer(int value) : m_value(value), m_timer(m_context, 1ms), m_thread(&AsioTimer::run, this)  
    {  
        m_timer.async_wait(bind(&AsioTimer::timeout, this));  
    }  
  
    ~AsioTimer()  
    {  
        auto running = true;  
        if (m_running.compare_exchange_strong(running, false))  
        {  
            if (m_timer.cancel() < 1)  
            {  
                cerr << "AsioTimer(" << m_value << ") timer cancel return 0" << endl;  
            }  
  
            m_context.stop();  
            if (m_thread.joinable())  
            {  
                m_thread.join();  
            }  
        }  
    }  
  
private:  
    void run()  
    {  
        while (m_running)  
        {  
            try  
            {  
                m_context.run();  
            }  
            catch (const exception &ex)  
            {  
                cerr << "run error: " << ex.what() << endl;  
            }  
        }  
    }  
  
    void timeout() { m_timer.async_wait(bind(&AsioTimer::timeout, this)); }  
  
    int m_value = 0;  
    io_context m_context;  
    steady_timer m_timer;  
    thread m_thread;  
    atomic_bool m_running = true;  
};  
  
TEST(AsioTimerCancel, test)  
{  
    auto count = 10000;  
    for (auto i = 0; i < count; ++i)  
    {  
        AsioTimer timer(i);  
        this_thread::sleep_for(1ms);  
    }  
}  
```   
[Associated](https://github.com/chriskohlhoff/asio/issues/1393)
