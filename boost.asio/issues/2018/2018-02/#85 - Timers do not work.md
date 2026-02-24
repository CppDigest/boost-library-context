# #85 - Timers do not work. [Closed]

> Username: heretic13  
> Created at: 2018-02-16 21:05:52 UTC  
> Updated at: 2018-02-17 09:24:33 UTC  
> Closed at: 2018-02-17 09:24:33 UTC  
> Url: https://github.com/boostorg/asio/issues/85  

Hello.  
My colleague showed me that the asio timers do not work as intended.  
This was tested on the OS Windows 10 64 bit .  
The following combinations are used (32-bit compilation):  
VS2008 SP1 + boost 1.65.1  
VS2017 + boost 1.66  
  
Tested timers - deadline_timer, steady_timer (for std / boost :: chrono). Everyone has problems.  
  
Below I put the source code, which raises questions about the behavior of timers.  
  
The error is often provoked when "long" processing in the timers handlers.  
  
The same error can be found on fewer timers (we managed to get an error with 2 timers and several tens of program starts/stops).  
  
The program works in one thread, this should exclude "racing".  
  
The output of the program leads to strange thoughts - how can a cancel () operation on a activated timer return 0?

---

## Comment 1

> Username: heretic13  
> Created at: 2018-02-16 21:06:59 UTC  
> Updated at: 2018-02-16 22:12:03 UTC  
> Url: https://github.com/boostorg/asio/issues/85#issuecomment-366358379  

`#define _WIN32_WINNT 0x0601  
  
#include < deque>  
#include < memory>  
  
#include <boost/asio.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <boost/bind.hpp>  
  
enum  
{  
	CheckPeriod = 1,  
};  
  
  
class Monitor  
{  
	boost::asio::io_service&	m_ios;  
	boost::asio::steady_timer	m_CheckTimer;  
  
	void priStart()  
	{  
		if (m_ios.stopped())  
			return;  
  
		puts("started");  
		RestartTimer(std::chrono::seconds(CheckPeriod));  
	}  
  
	void priStop()  
	{  
		if (m_ios.stopped())  
			return;  
  
		printf("%p - Monitor::priStop\n", this);  
		int ff = m_CheckTimer.cancel();  
		printf("%p - Monitor::priStop [%d]\n", this, ff);  
	}  
  
	template<class Rep, class Period>  
	void RestartTimer(const std::chrono::duration<Rep, Period>& dur)  
	{  
		printf("%p - Monitor::RestartTimer begin\n", this);  
  
		m_CheckTimer.expires_from_now(dur);  
		m_CheckTimer.async_wait(boost::bind(&Monitor::TimerHandler, this, boost::asio::placeholders::error));  
  
		printf("%p - Monitor::RestartTimer end\n", this);  
	}  
  
	void TimerHandler(const boost::system::error_code& error)  
	{  
		printf("%p - Monitor::TimerHandler [error code = %d]\n", this, error.value());  
  
		if (error == boost::asio::error::operation_aborted)  
		{  
			printf("%p - Monitor::TimerHandler aborted\n", this);  
			return;  
		}  
  
		std::this_thread::sleep_for(std::chrono::milliseconds(10));  
  
		RestartTimer( std::chrono::seconds(CheckPeriod) );  
	}  
  
public:  
  
	Monitor(boost::asio::io_service& ios):  
		m_ios(ios),  
		m_CheckTimer(m_ios)  
	{}  
  
	void Start()  
	{  
		m_ios.post(std::bind(&Monitor::priStart, this));  
	}  
  
	void Stop()  
	{  
		m_ios.post(std::bind(&Monitor::priStop, this));  
	}  
};  
  
  
  
void SignalHandler(const boost::system::error_code& error, int signal_number, std::deque< std::shared_ptr< Monitor> >& haspLicMonitorList)  
{  
	if (error == boost::asio::error::operation_aborted)  
		return;  
  
	printf("handling signal %d\n", signal_number);  
  
	for (auto p: haspLicMonitorList)  
	{  
		p->Stop();  
	}  
}  
  
int main()  
{  
	try  
	{  
		boost::asio::io_service io_service;  
		std::deque< std::shared_ptr< Monitor> > haspLicMonitorList;  
  
		for (size_t index = 0; index < 100; ++index)  
		{  
			auto ptrHaspLicMonitor=std::make_shared< Monitor>(io_service);  
			haspLicMonitorList.push_back(ptrHaspLicMonitor);  
			ptrHaspLicMonitor->Start();  
		}  
  
		//boost::asio::signal_set signals(io_service, SIGINT, SIGTERM);  
		//signals.async_wait(boost::bind(&SignalHandler, _1, _2, boost::ref(haspLicMonitorList)));  
  
		boost::asio::steady_timer tmDestroy(io_service);  
  
		tmDestroy.expires_from_now(std::chrono::seconds(5));  
		tmDestroy.async_wait([&](const boost::system::error_code& error)  
		{  
			puts("Trying to stop");  
  
			for (auto p : haspLicMonitorList)  
			{  
				p->Stop();  
			}  
		});  
  
  
		io_service.run();  
	}  
	catch (std::exception& e)  
	{  
		printf("Exception: %s\n", e.what());  
	}  
  
    return 0;  
}  
`

---

## Comment 2

> Username: heretic13  
> Created at: 2018-02-16 21:16:04 UTC  
> Url: https://github.com/boostorg/asio/issues/85#issuecomment-366360564  

Below is the output of the program and as a consequence, the program does not stop:  
  
02888000 - Monitor::priStop [1]  
02887BA0 - Monitor::priStop  
02887BA0 - Monitor::priStop [1]  
02888070 - Monitor::priStop  
02888070 - Monitor::priStop [1]  
02887900 - Monitor::priStop  
02887900 - Monitor::priStop [1]  
02887970 - Monitor::priStop  
02887970 - Monitor::priStop [1]  
028884D0 - Monitor::priStop  
028884D0 - Monitor::priStop [1]  
02888540 - Monitor::priStop  
02888540 - Monitor::priStop [1]  
028879E0 - Monitor::priStop  
028879E0 - Monitor::priStop [0]  
028882A0 - Monitor::priStop  
028882A0 - Monitor::priStop [0]  
02887C10 - Monitor::priStop  
02887C10 - Monitor::priStop [0]  
02887C80 - Monitor::priStop  
02887C80 - Monitor::priStop [0]  
028880E0 - Monitor::priStop  
028880E0 - Monitor::priStop [0]  
02888150 - Monitor::priStop  
02888150 - Monitor::priStop [0]  
02887CF0 - Monitor::priStop  
02887CF0 - Monitor::priStop [1]  
02887EB0 - Monitor::priStop  
02887EB0 - Monitor::priStop [1]  
02888230 - Monitor::priStop  
02888230 - Monitor::priStop [1]  
02887A50 - Monitor::priStop  
02887A50 - Monitor::priStop [1]  
02887F20 - Monitor::priStop

---

## Comment 3

> Username: heretic13  
> Created at: 2018-02-16 22:23:50 UTC  
> Url: https://github.com/boostorg/asio/issues/85#issuecomment-366375913  

Ubuntu 17.10 x64 (g++ 7.2.0) + boost 1.62  
Bug exists.

---

## Comment 4

> Username: heretic13  
> Created at: 2018-02-17 09:24:33 UTC  
> Url: https://github.com/boostorg/asio/issues/85#issuecomment-366428869  

All. Understood.  
"If the timer has already expired when cancel () is called, then the handlers for asynchronous wait operations will:  
  
have already been invoked; or  
have been queued for in the near future.  
These handlers can no longer be cancelled, and therefore are passed an error code that indicates the successful completion of the wait operation. "(Http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference /basic_waitable_timer/cancel/overload1.html)
