# #40 - thread safety of sin/cos [Closed]

> Username: bodomartin  
> Created at: 2018-01-26 15:04:29 UTC  
> Updated at: 2018-02-01 21:29:27 UTC  
> Closed at: 2018-01-31 14:55:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/40  

Hi,   
  
i am opening the issue here as a copy of the trac ticket #13251  
  
https://svn.boost.org/trac10/ticket/13251  
  
this is problem for me since boost 1.65.1

---

## Comment 1

> Username: NAThompson  
> Created at: 2018-01-30 17:19:23 UTC  
> Updated at: 2018-01-30 17:20:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/40#issuecomment-361666684  

Not sure how a call to a pure function can cause a race condition, but I've been surprised before so I ran it under ThreadSanitizer. No race conditions detected with this code:  
  
```  
#include <iostream>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <boost/math/special_functions/math_fwd.hpp>  
  
using boost::multiprecision::cpp_dec_float_50;  
  
int main()  
{  
    cpp_dec_float_50 x = 0.0000002;  
    cpp_dec_float_50 y = sin(x);  
    std::cout << std::setprecision(50);  
    std::cout << "y = " << y << std::endl;  
}  
```  
  
System info looks the same as yours:  
  
```  
Apple LLVM version 9.0.0 (clang-900.0.39.2)  
Target: x86_64-apple-darwin17.3.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
```  
  
Can you post a piece of code that reproduces the problem?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-01-30 18:00:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/40#issuecomment-361679668  

Thanks Nick, I think you would need to create some threads and hammer the sin/cos calls in each thread to see anything.  I've been meaning to try that but haven't had the time :(

---

## Comment 3

> Username: NAThompson  
> Created at: 2018-01-30 19:34:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/40#issuecomment-361708469  

This also ran under ThreadSanitizer without error:  
  
```  
#include <iostream>  
#include <thread>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <boost/math/special_functions/math_fwd.hpp>  
  
using boost::multiprecision::cpp_dec_float_50;  
  
void task()  
{  
      cpp_dec_float_50 x = 0.7;  
      size_t i = 0;  
      while(i++ < 500000)  
      {  
          x = cos(x);  
      }  
}  
  
int main()  
{  
    std::thread t0{task};  
    std::thread t1{task};  
    std::thread t2{task};  
    std::thread t3{task};  
    t0.join();  
    t1.join();  
    t2.join();  
    t3.join();  
}  
```

---

## Comment 4

> Username: bodomartin  
> Created at: 2018-01-31 14:55:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/40#issuecomment-361956448  

ok .. so it seems sin/cos are not the problem. The only difference to above is that I use boost threads; also what happens is not a crash but I sometimes get a slightly different value.   
Thanks - I will close the issue now

---

## Comment 5

> Username: NAThompson  
> Created at: 2018-01-31 15:17:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/40#issuecomment-361963455  

Ok, I also checked for UB and didn't see anything. If you manage to reproduce post the code.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2018-01-31 20:11:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/40#issuecomment-362055970  

I've been running this program for some time, with no failures so far:  
  
```  
#include <iostream>  
#include <thread>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <boost/math/special_functions/math_fwd.hpp>  
  
using boost::multiprecision::cpp_dec_float_50;  
  
std::vector<std::pair<cpp_dec_float_50, cpp_dec_float_50> > sins;  
std::vector<std::pair<cpp_dec_float_50, cpp_dec_float_50> > coss;  
  
void task()  
{  
   std::cout << "Starting task...." << std::endl;  
   for(unsigned i = 0; i < 100; ++i)  
   {  
     for(unsigned k = 0; k < sins.size(); ++i)  
     {  
        assert(sins[k].second == sin(sins[k].first));  
        assert(coss[k].second == cos(coss[k].first));  
     }  
   }  
   std::cout << "Ending task...." << std::endl;  
}  
  
int main()  
{  
    for(int i = -1000; i <= 1000; ++i)  
    {  
        cpp_dec_float_50 val(i);  
        val /= 519;  
        sins.push_back(std::make_pair(val, sin(val)));  
        coss.push_back(std::make_pair(val, cos(val)));  
    }  
    std::thread t0{task};  
    std::thread t1{task};  
    std::thread t2{task};  
    std::thread t3{task};  
    t0.join();  
    t1.join();  
    t2.join();  
    t3.join();  
    std::cout << "All tasks over..." << std::endl;  
}  
```

---

## Comment 7

> Username: jzmaddock  
> Created at: 2018-02-01 11:54:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/40#issuecomment-362243698  

Also tried to reproduce using Boost.Thread and everything in C++03 mode - still works for me.  So over to you I think.

---

## Comment 8

> Username: ckormanyos  
> Created at: 2018-02-01 21:29:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/40#issuecomment-362407868  

I'm planning to work out a stress test soon. Unfortunately, I do not have that compiler. I will report if I find anything helpful. Thanks.
