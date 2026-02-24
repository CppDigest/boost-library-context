# #16 - Wake up the sleeped fiber as soon as possible [Closed]

> Username: absolute8511  
> Created at: 2014-07-28 02:38:27 UTC  
> Updated at: 2015-02-26 12:50:01 UTC  
> Closed at: 2015-02-26 12:50:01 UTC  
> Url: https://github.com/boostorg/fiber/issues/16  

in   
void  
fiber_manager::run()  
{  
....  
            {  
                // no fibers ready to run; the thread should sleep  
                // until earliest fiber is scheduled to run  
                clock_type::time_point wakeup( next_wakeup() );  
                this_thread::sleep_until( wakeup);  
            }  
            return;  
}  
 while there is no fibers ready, the thread got sleep at least next_wakeup() time. I think there can be a better way to wait on some fd so that another thread can wake it up immediatly by write data to the fd.
