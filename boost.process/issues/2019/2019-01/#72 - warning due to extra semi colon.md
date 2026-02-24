# #72 - warning due to extra semi colon [Closed]

> Username: killerbot242  
> Created at: 2019-01-29 12:44:44 UTC  
> Updated at: 2019-03-21 05:06:31 UTC  
> Closed at: 2019-03-21 05:06:31 UTC  
> Url: https://github.com/boostorg/process/issues/72  

When including boost process header, warning pop up (-Wpedantic)  
There are semicolons present after the method definition (definition, not declaration)  
  
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
  
  
I have fixed this in ; https://github.com/boostorg/process/pull/71
