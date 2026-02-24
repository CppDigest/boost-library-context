# #165 - boost::posix_time::microsec_clock::local_time() and boost::posix_time::second_clock::local_time() mistmatch [Closed]

> Username: jcuevasdomingo  
> Created at: 2020-07-26 01:51:29 UTC  
> Updated at: 2020-07-27 20:33:32 UTC  
> Closed at: 2020-07-27 20:33:32 UTC  
> Url: https://github.com/boostorg/date_time/issues/165  

This code  
  
  auto loctime_1 = boost::posix_time::microsec_clock::local_time();  
  auto loctime_2 = boost::posix_time::second_clock::local_time();  
  auto seconds1 = loctime_1.time_of_day().total_seconds();  
  auto seconds2 = loctime_2.time_of_day().total_seconds();  
  
I would expect seconds1 and seconds2 to be always equal assuming that loctime_1 and loctime_are called withing the same second.  
  
On windows Embarcero Win32 clang C++ compiler I've found this to be not true when I set on Windows10 the time zone to Auckland,Wellington and the "Adjust for daylight saving time automatically" is on.  
  
If I set "Adjust for daylight saving time automatically" off it does work. In linux it does work. Is this a bug?

---

## Comment 1

> Username: jcuevasdomingo  
> Created at: 2020-07-27 20:33:32 UTC  
> Url: https://github.com/boostorg/date_time/issues/165#issuecomment-664623616  

I've found that the error is not in boost but a faulty std::local_time provided by my compiler.   
  
Closing the issue
