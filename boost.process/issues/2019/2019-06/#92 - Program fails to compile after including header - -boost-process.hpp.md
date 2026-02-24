# #92 - Program fails to compile after including header : <boost/process.hpp> [Open]

> Username: kkumar45  
> Created at: 2019-06-26 12:43:32 UTC  
> Updated at: 2020-10-06 12:50:06 UTC  
> Url: https://github.com/boostorg/process/issues/92  

I am facing an issue while upgrading to Boost 1.70.0. There are many files in my project that includes  <boost/process.hpp>.   
  
I have written a program that demonstrates the problem.   
  
```  
#include <boost/process.hpp>  
  
int main()  
{  
    return 0;  
}  
  
```  
Compilation line :   
`g++ -std=c++14 test_process.hpp -Iboost_1_70_0 -DBOOST_ASIO_ENABLE_OLD_SERVICES`  
  
My program fails to compile with below error:  
  
```  
In file included from boost_1_70_0/boost/process/detail/async_handler.hpp:16:0,  
                 from boost_1_70_0/boost/process/async_system.hpp:23,  
                 from boost_1_70_0/boost/process.hpp:24,  
                 from test_process.hpp:1:  
boost_1_70_0/boost/process/detail/posix/asio_fwd.hpp:33:46: error: conflicting declaration ‘typedef class boost::asio::basic_signal_set<boost::asio::signal_set_service> boost::asio::signal_set’  
 typedef basic_signal_set<signal_set_service> signal_set;  
                                              ^~~~~~~~~~  
In file included from boost_1_70_0/boost/process/detail/posix/sigchld_service.hpp:12:0,  
                 from boost_1_70_0/boost/process/detail/posix/io_context_ref.hpp:20,  
                 from boost_1_70_0/boost/process/async.hpp:42,  
                 from boost_1_70_0/boost/process.hpp:23,  
                 from test_process.hpp:1:  
boost_1_70_0/boost/asio/signal_set.hpp:25:28: note: previous declaration as ‘typedef class boost::asio::basic_signal_set<> boost::asio::signal_set’  
 typedef basic_signal_set<> signal_set;  
                            ^~~~~~~~~~  
In file included from boost_1_70_0/boost/process/detail/async_handler.hpp:16:0,  
                 from boost_1_70_0/boost/process/async_system.hpp:23,  
                 from boost_1_70_0/boost/process.hpp:24,  
                 from test_process.hpp:1:  
boost_1_70_0/boost/process/detail/posix/asio_fwd.hpp:50:60: error: conflicting declaration ‘typedef class boost::asio::posix::basic_stream_descriptor<boost::asio::posix::stream_descriptor_service> boost::asio::posix::stream_descriptor’  
 typedef basic_stream_descriptor<stream_descriptor_service> stream_descriptor;  
                                                            ^~~~~~~~~~~~~~~~~  
In file included from boost_1_70_0/boost/process/detail/posix/async_pipe.hpp:11:0,  
                 from boost_1_70_0/boost/process/async_pipe.hpp:18,  
                 from boost_1_70_0/boost/process/detail/posix/async_in.hpp:16,  
                 from boost_1_70_0/boost/process/async.hpp:43,  
                 from boost_1_70_0/boost/process.hpp:23,  
                 from test_process.hpp:1:  
boost_1_70_0/boost/asio/posix/stream_descriptor.hpp:30:35: note: previous declaration as ‘typedef class boost::asio::posix::basic_stream_descriptor<> boost::asio::posix::stream_descriptor’  
 typedef basic_stream_descriptor<> stream_descriptor;  
```

---

## Comment 1

> Username: kkumar45  
> Created at: 2019-07-04 10:05:04 UTC  
> Url: https://github.com/boostorg/process/issues/92#issuecomment-508424623  

Can anyone reproduce it?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2019-07-10 09:13:32 UTC  
> Url: https://github.com/boostorg/process/issues/92#issuecomment-509980897  

I updated boost.process to work woth the current asio interface and dropped support for the old classes. Well at least my forward declarations don't support that, maybe the easiest one is to just comment them out.

---

## Comment 3

> Username: kkumar45  
> Created at: 2019-07-30 11:40:54 UTC  
> Url: https://github.com/boostorg/process/issues/92#issuecomment-516382920  

Thanks

---

## Comment 4

> Username: kkumar45  
> Created at: 2019-08-07 05:33:29 UTC  
> Url: https://github.com/boostorg/process/issues/92#issuecomment-518946868  

@klemens-morgenstern will this be fixed in next boost version ?

---

## Comment 5

> Username: HLXEasy  
> Created at: 2020-10-04 18:48:55 UTC  
> Url: https://github.com/boostorg/process/issues/92#issuecomment-703298805  

Any news on this? Stumbling into the same issue if I try to compile with 1.73 on a Mac. Same code with 1.68.0_1 builds without issues.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2020-10-06 02:20:47 UTC  
> Url: https://github.com/boostorg/process/issues/92#issuecomment-703988999  

There were some more asio Api changes we adapted. I hope that fixed the issue by now.

---

## Comment 7

> Username: HLXEasy  
> Created at: 2020-10-06 12:50:06 UTC  
> Url: https://github.com/boostorg/process/issues/92#issuecomment-704246260  

@klemens-morgenstern Thx for the response! So that's on release 1.74 I assume? Need to check, if/when homebrew-boost get's updated then...
