# #31 Fix #12040: Order of includes should not matter [Closed]

> Username: stac47  
> Created at: 2016-03-04 04:24:26 UTC  
> Updated at: 2016-11-03 19:58:59 UTC  
> Closed at: 2016-11-03 19:58:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/31  

The compilation success of a program should not rely upon the order in which a developer includes the headers. This is unfortunately the case when we want to serialize an object in which one of its member is a boost::gregorian::date.  
  
For the time being, a developer must include <boost/date_time/gregorian/gregorian.hpp> before <boost/date_time/gregorian/grep_serialize.hpp> otherwise the following error is thrown (with gcc, but it is reproduceable in clang):  
  
```  
[18:34]stac@macdebian:~/development/cpp-sandbox/boost>g++ --std=c++14 -g -I$HOME/development/date_time/include -lboost_serialization -lboost_date_time serialization.cpp  
In file included from serialization.cpp:6:0:  
/home/stac/development/date_time/include/boost/date_time/gregorian/greg_serialize.hpp: In function ‘void boost::serialization::save(Archive&, const boost::gregorian::date&, unsigned int)’:  
/home/stac/development/date_time/include/boost/date_time/gregorian/greg_serialize.hpp:59:35: error: there are no arguments to ‘to_iso_string’ that depend on a template parameter, so a declaration of ‘to_iso_string’ must be available [-fpermissive]  
   std::string ds = to_iso_string(d);  
                                   ^  
/home/stac/development/date_time/include/boost/date_time/gregorian/greg_serialize.hpp:59:35: note: (if you use ‘-fpermissive’, G++ will accept your code, but allowing the use of an undeclared name is deprecated)  
zsh: exit 1     g++ --std=c++14 -g -I$HOME/development/date_time/include -lboost_serializatio  
```  
  
Current architecture:   
Linux macdebian 4.3.0-1-amd64 #1 SMP Debian 4.3.5-1 (2016-02-06) x86_64 GNU/Linux  
  
Compilers:  
gcc: g++ (Debian 5.3.1-8) 5.3.1 20160205  
  
clang: Debian clang version 3.6.2-3 (tags/RELEASE_362/final) (based on LLVM 3.6.2) Target: x86_64-pc-linux-gnu Thread model: posix  
  
A solution could be to forward declare the boost::gregorian::to_iso_string(const boost::gregorian::date&) in greg_serialize.hpp. Or to include gregorian/formatters[_limited].hpp  
  
Remark: This problem does not occur with posix_time.

---

## Comment 1

> Username: mclow  
> Created_at: 2016-03-07 18:56:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/31#issuecomment-193395062  

The whitespace changes that you've made here are obscuring the change that you actually want.  
I'd also like to see a test here - one that failed w/o the patch, and succeeded afterwards.

---

## Comment 2

> Username: stac47  
> Created_at: 2016-03-08 07:35:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/31#issuecomment-193649555  

Ok I will split in several commits and add a test. Thanks for your feedback.

---

## Comment 3

> Username: Belcourt  
> Created_at: 2016-11-03 19:58:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/31#issuecomment-258256592  

Add forward declaration of to_iso_string into boost gregorian namespace.

---
