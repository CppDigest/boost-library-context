# #10 - Bad link to (missing) example [Closed]

> Username: user595  
> Created at: 2018-08-20 09:31:22 UTC  
> Updated at: 2019-01-16 18:34:49 UTC  
> Closed at: 2019-01-16 18:34:49 UTC  
> Url: https://github.com/boostorg/timer/issues/10  

The link to an example for timers is bad. On the page   https://www.boost.org/doc/libs/1_68_0/libs/timer/doc/cpu_timers.html    the example listed,    https://www.boost.org/doc/libs/1_68_0/libs/timer/example/auto_cpu_example.cpp    goes to a message that the file is missing.  
  
I've created an example that could be placed there. I've copied the attribution information from other examples for boost.  
  
  
//  Copyright Dwight Wilkins 2018  
//  Dwight Wilkins 2018 DwightWilkins@gmail.com   
//  Distributed under the Boost Software License, Version 1.0.  
//  See http://www.boost.org/LICENSE_1_0.txt  
//  See http://www.boost.org/libs/timer for documentation.  
  
#include <boost/timer/timer.hpp>  
#include <iostream>  
  
void test()   
	{  
		boost::timer::auto_cpu_timer t2(std::cerr, 10, "\n\n\n%w wall %u user %s system %t user   system %p%\n");  
		  
		for (int n = 0; n != 200000000; ++n) continue;  
	}  
  
	int main()  
	{  
		boost::timer::auto_cpu_timer t(std::clog,5,"\n\n\nEnding program %w times.wall %u times.user %s times.system %t times.user   times.system %p%\n");  
  
		for (int i = 0; i < 3; i++) test();  
	}

---

## Comment 1

> Username: pdimov  
> Created at: 2019-01-16 18:34:42 UTC  
> Url: https://github.com/boostorg/timer/issues/10#issuecomment-454889910  

I believe that this was fixed by https://github.com/boostorg/timer/pull/9.
