# #199 - round_robin not round? [Closed]

> Username: miniframework  
> Created at: 2019-04-17 11:02:16 UTC  
> Updated at: 2019-04-19 03:34:28 UTC  
> Closed at: 2019-04-18 07:03:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/199  

output:  
main thread started 140574450718592  
fiber 1 started on thread 140574450718592   
fiber 2 started on thread 140574450718592  
fiber 3 started on thread 140574450718592  
fiber 4 started on thread 140574450718592  
fiber 5 started on thread 140574450718592  
fiber 6 started on thread 140574450718592  
fiber 7 started on thread 140574450718592  
fiber 8 started on thread 140574450718592  
fiber 9 started on thread 140574450718592  
  
Running in only one thread 140574450718592  
  
my program problems？ or  understanding problems ?   help me.  
  
```  
  
void whatevah( char me) {  
    try {  
        std::thread::id my_thread = std::this_thread::get_id(); /*< get ID of initial thread >*/  
        {  
            std::ostringstream buffer;  
            buffer << "fiber " << me << " started on thread " << my_thread ;  
            std::cout << buffer.str() << " "<< my_thread << std::endl <<std::flush;  
        }  
    } catch ( ... ) {  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
}  
void thread() {  
    std::ostringstream buffer;  
    buffer << "thread started " << std::this_thread::get_id() << std::endl;  
    std::cout << buffer.str() << std::flush;  
    boost::fibers::use_scheduling_algorithm< boost::fibers::algo::round_robin >( );  
    lock_type lk( mtx_count);  
    cnd_count.wait( lk, [](){ return 0 == fiber_count; } );  
}  
int main( int argc, char *argv[]) {  
    std::cout << "main thread started " << std::this_thread::get_id() << std::endl;  
    for ( char c : std::string("123456789")) {  
        boost::fibers::fiber([c](){ whatevah( c); }).detach();  
        ++fiber_count;  
    }  
    std::thread threads[] = {  
        std::thread( thread),  
        std::thread( thread),  
        std::thread( thread)  
    };  
    boost::fibers::use_scheduling_algorithm< boost::fibers::algo::round_robin >( );  
    {  
        lock_type lk( mtx_count);  
        cnd_count.wait( lk, [](){ return 0 == fiber_count; } );  
    }  
    for ( std::thread & t : threads) {  
        t.join();  
    }  
    std::cout << "done." << std::endl;  
    return EXIT_SUCCESS;  
}  
  
```

---

## Comment 1

> Username: olk  
> Created at: 2019-04-17 11:38:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/199#issuecomment-484047561  

only one core?

---

## Comment 2

> Username: miniframework  
> Created at: 2019-04-17 11:53:48 UTC  
> Updated at: 2019-04-17 11:55:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/199#issuecomment-484051567  

> only one core?  
  
no,  mult core ,   stealing and shared   no problem.  
  
or  my code problems?

---

## Comment 3

> Username: miniframework  
> Created at: 2019-04-18 02:56:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/199#issuecomment-484337089  

> only one core?  
main thread started 139732827944832  
thread started 139732810585856  
fiber 1 started on thread 139732827944832  
fiber 2 started on thread 139732827944832  
fiber 3 started on thread 139732827944832  
fiber 4 started on thread 139732827944832  
fiber 5 started on thread 139732827944832  
fiber 6 started on thread 139732827944832  
fiber 7 started on thread 139732827944832  
fiber 8 started on thread 139732827944832  
fiber 9 started on thread 139732827944832  
thread started 139732802193152  
thread started 139732793800448  
done.  
  
all always run in main thread .   what's problem?

---

## Comment 4

> Username: olk  
> Created at: 2019-04-18 07:03:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/199#issuecomment-484381480  

round-robin does not work-sharing or work-stealing  - how else should other threads get the fibers?!

---

## Comment 5

> Username: miniframework  
> Created at: 2019-04-19 03:32:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/199#issuecomment-484756983  

round-robin  what's meaning？  not only main thread run fiber .     
  
fiber run in threads  one by one . example:   
  
main thread started 139732827944832  
thread started 139732810585856  
thread started 139732802193152  
thread started 139732793800448  
fiber 1 started on thread 139732827944832  
fiber 2 started on thread 139732810585856  
fiber 3 started on thread 139732802193152  
fiber 4 started on thread 139732793800448

---

## Comment 6

> Username: miniframework  
> Created at: 2019-04-19 03:34:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/199#issuecomment-484757184  

give me a round-robin  demo  ?   
libs/fiber//example   only  work-sharing and work-stealing demo
