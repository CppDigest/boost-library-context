# #64 trac-11168: special_values handling in time duration helpers [Merged]

> Username: jeking3  
> Created at: 2017-12-28 19:48:48 UTC  
> Updated at: 2017-12-31 05:42:28 UTC  
> Merged at: 2017-12-31 05:42:28 UTC  
> Closed at: 2017-12-31 05:42:28 UTC  
> Url: https://github.com/boostorg/date_time/pull/64  

https://svn.boost.org/trac10/ticket/11168  
  
Ensure special values are not automatically translated to integral types in the hours, minutes, seconds, and fractional seconds time duration helpers.   For example:  
  
```  
// pos_infin is an enum which converts to long automagically  
time_duration dur = seconds(pox_infin);  
assert(dur.total_seconds() == 2);  
```  
  
Obviously the special_values should not be used in this way, but the compiler wasn't failing it.  
This approach as suggested in trac-11168 is to use SFINAE to fix that.  I was able to do hours(), minutes(), seconds(), and fractional seconds helpers.  If you try to use a special_values value:  
  
```  
clang-linux.compile.c++.without-pth ../../../bin.v2/libs/date_time/test/minutes_special_value.test/clang-gnu-linux-5.0.0/debug/cxxstd-17-iso/threadapi-pthread/minutes_special_value.o  
posix_time/compile_fail/minutes_special_value.cpp:17:26: error: no matching conversion for functional-style cast from 'boost::date_time::special_values' to 'boost::posix_time::minutes'  
    time_duration dur2 = minutes(pos_infin);                               // won't compile: not an integral  
                         ^~~~~~~~~~~~~~~~~  
../../../boost/date_time/posix_time/posix_time_duration.hpp:39:30: note: candidate constructor (the implicit copy constructor) not viable: no known conversion from 'boost::date_time::special_values' to 'const boost::posix_time::minutes' for 1st argument  
  class BOOST_SYMBOL_VISIBLE minutes : public time_duration  
                             ^  
../../../boost/date_time/posix_time/posix_time_duration.hpp:39:30: note: candidate constructor (the implicit move constructor) not viable: no known conversion from 'boost::date_time::special_values' to 'boost::posix_time::minutes' for 1st argument  
../../../boost/date_time/posix_time/posix_time_duration.hpp:44:37: note: candidate template ignored: disabled by 'enable_if' [with T = boost::date_time::special_values]  
          typename boost::enable_if<boost::is_integral<T>, void>::type* = 0) :  
                                    ^  
1 error generated.  
(failed-as-expected) ../../../bin.v2/libs/date_time/test/minutes_special_value.test/clang-gnu-linux-5.0.0/debug/cxxstd-17-iso/threadapi-pthread/minutes_special_value.o  
```  
  
Note this PR does not tackle time_duration itself, which can still be confused by:  
  
```  
time_duration dur(0, 0, pos_infin, 0);   
assert(dur.total_seconds() == 2);  
```  
  
It wasn't trivial to fix this, however this is an improvement, so I think it stands on its own, assuming folks are okay with it.

---
