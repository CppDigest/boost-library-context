# #22 Fix compilation issue with -fno-exceptions [Closed]

> Username: jacquelinekay  
> Created at: 2017-07-26 13:20:37 UTC  
> Updated at: 2017-08-08 09:20:15 UTC  
> Closed at: 2017-08-08 09:20:11 UTC  
> Url: https://github.com/boostorg/signals2/pull/22  

Double-posting from the mailing list:  
  
Trying to compile any program that includes boost/signals2 with -fno-exceptions fails as of 1.64:  
  
#include <boost/signals2.hpp>  
// boost/signals2/slot.hpp will also reproduce the error  
  
int main()  
{  
}  
  
g++ prog.cc -I<path to Boost 1.64.0 headers> -std=c++11 -fno-exceptions  
  
View it on Wandbox:  
https://wandbox.org/permlink/vZVpF5bb8E9K7sDz  
  
I was also able to reproduce this locally on the develop branch.  
  
I'm not sure if avoiding the lock functions when exceptions are turned off is a good idea or not, since I'm not intimately familiar with the inner workings of Signals2. Feedback welcome. I was also unable to figure out how to build the test suite for this library because I'm not familiar with Boost.Build.

---

## Comment 1

> Username: fmhess  
> Created_at: 2017-07-27 15:12:16 UTC  
> Url: https://github.com/boostorg/signals2/pull/22#issuecomment-318392166  

I don't think your patch it quite the right way to fix the problem.  I've committed an alternate fix to develop, would you try that?

---

## Comment 2

> Username: jacquelinekay  
> Created_at: 2017-07-28 09:18:44 UTC  
> Updated_at: 2017-07-28 09:18:52 UTC  
> Url: https://github.com/boostorg/signals2/pull/22#issuecomment-318604061  

Thanks for taking a look at this!  
  
I now get the following error from develop:  
  
```  
clang++-3.9 signals2.cpp -std=c++11 -I/home/kayj/signals2/include -fno-exceptions  
In file included from signals2.cpp:1:  
In file included from /home/kayj/signals2/include/boost/signals2.hpp:16:  
/home/kayj/signals2/include/boost/signals2/deconstruct_ptr.hpp:43:9: error:   
      cannot use 'try' with exceptions disabled  
        try  
        ^  
In file included from signals2.cpp:1:  
In file included from /home/kayj/signals2/include/boost/signals2.hpp:18:  
/home/kayj/signals2/include/boost/signals2/last_value.hpp:55:9: error: cannot use  
      'throw' with exceptions disabled  
        throw no_slots_error();  
        ^  
2 errors generated.  
```  
I rebased this branch and tried fixing it by using boost::throw_exception and conditionally disabling try/catch if BOOST_NOEXCEPT is defined in deconstruct_ptr. Is this a proper use of the Boost exception handling tools?

---

## Comment 3

> Username: fmhess  
> Created_at: 2017-07-28 16:16:30 UTC  
> Url: https://github.com/boostorg/signals2/pull/22#issuecomment-318697383  

You should use the BOOST_TRY/BOOST_CATCH/BOOST_CATCH_END macros in boost/core/no_exceptions_support.hpp rather than checking BOOST_NOEXCEPT.  I've done this and pushed the changes to develop.  Would you try this develop head again?

---

## Comment 4

> Username: jacquelinekay  
> Created_at: 2017-08-08 09:20:11 UTC  
> Url: https://github.com/boostorg/signals2/pull/22#issuecomment-320900512  

Yes, it works now! Sorry for the delay in review and thank you very much for fixing this.

---
