# #107 - group::wait_for and group::wait_until are broken [Open]

> Username: omusil24  
> Created at: 2019-10-25 12:18:55 UTC  
> Updated at: 2019-10-26 04:42:55 UTC  
> Url: https://github.com/boostorg/process/issues/107  

# Description  
  
If `group::wait_for` is called with some timeout value, and times out without the child process exiting during that time, it degrades into blocking wait. Obviously same happens when calling `wait_until` as `wait_for` is just a wrapper for it.  
  
Affects all Boost versions after 1.69.00.  
  
Happens when `BOOST_POSIX_HAS_SIGTIMEDWAIT` is defined.  
  
# How to reproduce  
  
Build and run the following test program:  
```  
#include <iostream>  
#include <chrono>  
#include <string>  
#include <vector>  
  
#include <boost/process.hpp>  
  
namespace bp = boost::process;  
  
using namespace std;  
using namespace std::chrono;  
  
int main() {  
    vector<string> args;  
    args.push_back("5");  
  
    bp::group g;  
  
    auto t1 = steady_clock::now();  
    bp::child c(  
        bp::exe = "/usr/bin/sleep",  
        bp::args = args,  
        g  
    );  
  
    auto t2 = steady_clock::now();  
    g.wait_for(milliseconds(100));  
    auto t3 = steady_clock::now();  
  
    duration<double> dur = duration_cast<duration<double>>(t3 - t2);  
  
    cerr << "t1: " << t1.time_since_epoch().count() << endl;  
    cerr << "t2: " << t2.time_since_epoch().count() << endl;  
    cerr << "t3: " << t3.time_since_epoch().count() << endl;  
    cerr << "It took " << dur.count() << " seconds." << endl;  
  
    return 0;  
}  
```  
  
# Output without fix  
  
```  
[omusil@borg src]$ g++ -I../boost_dir -lpthread prtest.cpp   
[omusil@borg src]$ ./a.out   
t1: 115006087159443  
t2: 115006087613681  
t3: 115011088213691  
It took 5.0006 seconds.  
```  
  
# Output with fix  
  
```  
[omusil@borg src]$ g++ -I../boost_dir -lpthread prtest.cpp   
[omusil@borg src]$ ./a.out   
t1: 115041253522727  
t2: 115041253977025  
t3: 115041354057817  
It took 0.100081 seconds.  
```  
  
# Fix  
  
Add the following lines after `ret = ::sigtimedwait(&sigset, nullptr, &ts);` call in the `wait_until` function:  
```  
if ((ret == -1) && (errno == EAGAIN)) { // return if timed out  
    ec.clear();  
    return false;  
}  
```  
  
# How it should look after fix  
```  
template< class Clock, class Duration >  
inline bool wait_until(  
        const group_handle &p,  
        const std::chrono::time_point<Clock, Duration>& time_out,  
        std::error_code & ec) noexcept  
{  
  
    ::siginfo_t siginfo;  
  
    bool timed_out = false;  
    int ret;  
  
#if defined(BOOST_POSIX_HAS_SIGTIMEDWAIT)  
    auto get_timespec =  
            +[](const Duration & dur)  
            {  
                ::timespec ts;  
                ts.tv_sec  = std::chrono::duration_cast<std::chrono::seconds>(dur).count();  
                ts.tv_nsec = std::chrono::duration_cast<std::chrono::nanoseconds>(dur).count() % 1000000000;  
                return ts;  
            };  
  
    ::sigset_t  sigset;  
  
    if (sigemptyset(&sigset) != 0)  
    {  
        ec = get_last_error();  
        return false;  
    }  
    if (sigaddset(&sigset, SIGCHLD) != 0)  
    {  
        ec = get_last_error();  
        return false;  
    }  
  
    struct ::sigaction old_sig;  
    if (-1 == ::sigaction(SIGCHLD, nullptr, &old_sig))  
    {  
        ec = get_last_error();  
        return false;  
    }  
  
    do  
    {  
        auto ts = get_timespec(time_out - Clock::now());  
        ret = ::sigtimedwait(&sigset, nullptr, &ts);  
        if ((ret == -1) && (errno == EAGAIN)) { // return if timed out  
            ec.clear();  
            return false;  
        }  
        errno = 0;  
        if ((ret == SIGCHLD) && (old_sig.sa_handler != SIG_DFL) && (old_sig.sa_handler != SIG_IGN))  
            old_sig.sa_handler(ret);  
  
        ret = ::waitpid(-p.grp, &siginfo.si_status, 0); //so in case it exited, we wanna reap it first  
        if (ret == -1)  
        {  
			if ((errno == ECHILD) || (errno == ESRCH))  
			{  
				ec.clear();  
				return true;  
			}  
			else  
			{  
				ec = get_last_error();  
				return false;   
			}  
        }  
  
        //check if we're done ->  
        ret = ::waitid(P_PGID, p.grp, &siginfo, WEXITED | WNOHANG);  
    }  
    while (((ret != -1) || ((errno != ECHILD) && (errno != ESRCH))) && !(timed_out = (Clock::now() > time_out)));  
  
    if (errno != ECHILD)  
    {  
        ec = boost::process::detail::get_last_error();  
        return !timed_out;  
    }  
    else  
    {  
        ec.clear();  
        return true; //even if timed out, there are no child proccessess left  
    }  
  
#else  
    ::timespec sleep_interval;  
    sleep_interval.tv_sec = 0;  
    sleep_interval.tv_nsec = 1000000;  
  
  
    while (!(timed_out = (Clock::now() > time_out)))  
    {  
        ret = ::waitid(P_PGID, p.grp, &siginfo, WEXITED | WSTOPPED | WNOHANG);  
        if (ret == -1)  
        {  
            if ((errno == ECHILD) || (errno == ESRCH))  
            {  
                ec.clear();  
                return true;  
            }  
            ec = boost::process::detail::get_last_error();  
            return false;  
        }  
        //we can wait, because unlike in the wait_for_exit, we have no race condition regarding eh exit code.  
        ::nanosleep(&sleep_interval, nullptr);  
    }  
    return !timed_out;  
#endif  
}  
```  
  
Thank you.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-10-26 04:05:27 UTC  
> Url: https://github.com/boostorg/process/issues/107#issuecomment-546566103  

Thanks for the report, seems I missed the obvious timeout. Tests should be running on the develop branch now.

---

## Comment 2

> Username: omusil24  
> Created at: 2019-10-26 04:13:12 UTC  
> Url: https://github.com/boostorg/process/issues/107#issuecomment-546566788  

@klemens-morgenstern also, I noticed that the SIGCHLD handler is unset at the beginning of this function in the sigtimedwait case but it's never set back to it's previous state. Isn't that a problem?  
  
Also, it seems like this is not thread-safe since one wait call changes the handlers and then another call could change it in another way.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2019-10-26 04:42:55 UTC  
> Url: https://github.com/boostorg/process/issues/107#issuecomment-546568357  

I fear you're right about the reset. I'll need to add some tests.  
  
I gotta check the thread-safety, but I think that'll be alright since the handler forwards to the previous one. I think the OS locks that internally (will check).  
  
It's gonna take me a while though, so I would happily accept PRs if I am too slow.
