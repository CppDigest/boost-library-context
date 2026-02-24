# #168 - POSIX wait_for() crashes when multiple threads run it simultaneously [Open]

> Username: martlaak-gmail  
> Created at: 2020-08-18 06:57:10 UTC  
> Updated at: 2020-10-16 13:33:28 UTC  
> Url: https://github.com/boostorg/process/issues/168  

Seems POSIX wait_for() is not thread-safe. Following simple program crashes:  
  
```  
#include <boost/process/child.hpp>  
#include <thread>  
  
using namespace std;  
namespace bp = boost::process;  
  
void runProcess()  
{  
  unique_ptr<bp::child> requestProcess(new bp::child("\"echo test\"", bp::shell));  
  while (requestProcess->running())  
    requestProcess->wait_for(chrono::seconds(1));  
}  
  
int main()  
{  
  std::thread thread1(runProcess);  
  std::thread thread2(runProcess);  
  thread1.join();  
  thread2.join();  
}  
```  
With stacktrace:  
```  
Program terminated with signal SIGSEGV, Segmentation fault.  
#0  0x0000559ed51d75b3 in boost::process::detail::posix::wait_until<std::chrono::_V2::steady_clock, std::chrono::duration<long, std::ratio<1l, 1000000000l> > >(boost::process::detail::posix::child_handle const&, int&, std::chrono::time_point<std::chrono::_V2::steady_clock, std::chrono::duration<long, std::ratio<1l, 1000000000l> > > const&, std::error_code&)::signal_interceptor_t::handler_func(int) (  
    val=17127) at ../../boost_1_74_0-native/include/boost/process/detail/posix/wait_for_exit.hpp:69  
#1  0x0000559ed51d75b5 in boost::process::detail::posix::wait_until<std::chrono::_V2::steady_clock, std::chrono::duration<long, std::ratio<1l, 1000000000l> > >(boost::process::detail::posix::child_handle const&, int&, std::chrono::time_point<std::chrono::_V2::steady_clock, std::chrono::duration<long, std::ratio<1l, 1000000000l> > > const&, std::error_code&)::signal_interceptor_t::handler_func(int) (  
    val=17127) at ../../boost_1_74_0-native/include/boost/process/detail/posix/wait_for_exit.hpp:69  
...  
```  
  
Notes:  
- same code with requestProcess->wait() does not crash  
- same code works on older Boost::Process (like with version 1.67)

---

## Comment 1

> Username: martin5233  
> Created at: 2020-09-18 10:46:22 UTC  
> Url: https://github.com/boostorg/process/issues/168#issuecomment-694796924  

I stumbled over the same problem, which prevents the upgrade from 1.67 to 1.74 in my case. I can supply a slightly different example, which uses std::async, but in principle it does the same thing as in the original bug report.

---

## Comment 2

> Username: griwes  
> Created at: 2020-10-04 07:17:44 UTC  
> Url: https://github.com/boostorg/process/issues/168#issuecomment-703214373  

This is even worse than the previous issue I had with timed waits with this library... at least that one just made things run slower, as opposed to segfaulting by overflowing the stack.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2020-10-06 02:19:09 UTC  
> Url: https://github.com/boostorg/process/issues/168#issuecomment-703988587  

The issues is noted, I fear I might need to remove those functions altogether though. The only solution that seems to work is to fork a timeout process as it currently does on osx. linux currently uses sigtimedwait which introduced a nasty race condition you're running into.  
  
But even the fork solution seems unreliable (at least on osx) since the timeout might not trigger at all.  
  
I would of course be interested if anyone has ideas on how to implement the timed wait functions, because I can't seem to figure out a portable solution.

---

## Comment 4

> Username: martin5233  
> Created at: 2020-10-16 13:33:28 UTC  
> Url: https://github.com/boostorg/process/issues/168#issuecomment-710050105  

I think there are two separate use cases for wait_for here:  
  
1. Providing a short time period in order to regularly check for some external condition, while still waiting for the result of the subprocess.  
2. Providing a long time period, which would be the limit, how long you would like to wait at most for the result of the subprocess.  
  
The first case could be handled by something like this:  
```  
while (c.running())  
{  
   sleep(<short time>);  
   checkExternalCondition();  
}  
```  
  
In this case it does not really matter, if it takes somewhat longer to detect, that the child process has terminated.  
  
However this would not work for the second case. Unfortunately I cannot recommend any solution for this, as I do not have much experience with those more specialized signal handling functions. As my use case is the first one, I will go for a solution based on repeatedly checking, if the subprocess is still running.
