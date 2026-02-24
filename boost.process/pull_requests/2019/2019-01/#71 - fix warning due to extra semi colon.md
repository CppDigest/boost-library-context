# #71 fix warning due to extra semi colon [Merged]

> Username: killerbot242  
> Created at: 2019-01-29 12:41:10 UTC  
> Updated at: 2019-02-03 13:19:46 UTC  
> Merged at: 2019-02-03 13:19:46 UTC  
> Closed at: 2019-02-03 13:19:46 UTC  
> Url: https://github.com/boostorg/process/pull/71  

In file included from /home/ldco/boost/include/boost/process/detail/on_exit.hpp:12:0,  
                 from /home/ldco/boost/include/boost/process/async.hpp:33,  
                 from /home/ldco/boost/include/boost/process.hpp:23,  
                 from /home/ldco/Projects/Teaching/Teaching/Boost/Process/Process1/src/main.cpp:7:  
/home/ldco/boost/include/boost/process/detail/posix/on_exit.hpp:30:6: warning: extra ‘;’ [-Wpedantic]  
     };  
      ^  
In file included from /home/ldco/boost/include/boost/process/async.hpp:42:0,  
                 from /home/ldco/boost/include/boost/process.hpp:23,  
                 from /home/ldco/Projects/Teaching/Teaching/Boost/Process/Process1/src/main.cpp:7:  
/home/ldco/boost/include/boost/process/detail/posix/io_context_ref.hpp:67:6: warning: extra ‘;’ [-Wpedantic]  
     };  
      ^

---
