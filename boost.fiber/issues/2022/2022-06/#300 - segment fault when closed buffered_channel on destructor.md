# #300 - segment fault when closed buffered_channel on destructor [Open]

> Username: arc130  
> Created at: 2022-06-15 01:59:26 UTC  
> Updated at: 2022-06-15 01:59:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/300  

how to solve this problem, the backtrace is shown below:  
Program terminated with signal SIGSEGV, Segmentation fault.   
#0 0x0000556eaf27c01c in boost::fibers::context::wake (this=0x7fla20000b60, epoch=<optimized out) at./boost/fiber/context.hpp:252   
#1 0x0000556eaf27ceec in boost::fibers::waker::wake (this=<optimized out>) at libs/fiber/src/waker.cpp:61   
#2 boost::fibers::wait_queue::notify_all(this=0x556eaf816d40 <getFibersPool2instance()::pools+96>) at libs/fiber/src/waker.cpp:61   
#3 0x0000556eaf0c27d3 in boost::fibers::buffered_channel<std:function<void ()>>::close()( this=0x556eaf816d28 <getFibersPool2instance():pools+72*) at /usr/local/include/boost/fiber/buffered _channel.hpp:92
