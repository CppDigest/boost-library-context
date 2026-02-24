# #126 - High memory usage in `dispatch` to `thread_pool` [Closed]

> Username: Shians  
> Created at: 2018-06-29 02:16:47 UTC  
> Updated at: 2019-02-14 00:48:14 UTC  
> Closed at: 2019-02-14 00:48:14 UTC  
> Url: https://github.com/boostorg/asio/issues/126  

I am trying to use `asio::thread_pool` for general computation, and though I am finding a really nice speedup, I am also finding that it uses large amounts of memory dependent of number of tasks dispatched.  
  
    #include <iostream>  
    #include <vector>  
    #include <boost/asio/thread_pool.hpp>  
    #include <boost/asio/dispatch.hpp>  
  
    using namespace std;  
  
    boost::asio::thread_pool t_pool(4);  
  
    int main() {  
        for (int run = 0; run < 100'000; run++) {  
            boost::asio::dispatch(t_pool,  
                [] () {}  
            );  
            t_pool.join();  
        }  
    }  
  
Here `time -v` tells me `Maximum resident set size (kbytes): 4264`. If I increase the number of loops to 1000000: `for (int run = 0; run < 1'000'000; run++)` then `time -v` repots `Maximum resident set size (kbytes): 32812`. As you can see the tasks dispatched do absolutely nothing and they are waited for by the end of each loop. Naively I would think that no memory associated with the previous task should remain when the next loop starts.

---

## Comment 1

> Username: djarek  
> Created at: 2018-06-29 20:46:54 UTC  
> Updated at: 2018-06-29 20:49:37 UTC  
> Url: https://github.com/boostorg/asio/issues/126#issuecomment-401469758  

@Shians   
  
`thread_pool::join()` waits until all threads running in the thread pool are joined. Threads are only created in the thread pool's constructor. Therefore this code works as designed - you can't expect operations posted onto a `thread_pool` to be executed if no threads are running!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-06-29 20:50:47 UTC  
> Updated at: 2018-06-29 20:50:58 UTC  
> Url: https://github.com/boostorg/asio/issues/126#issuecomment-401470594  

Your `main` should look like this:  
```  
int main()  
{  
    for (int run = 0; run < 100'000; run++)  
        boost::asio::dispatch(t_pool, []{});  
    t_pool.join();  
}  
```

---

## Comment 3

> Username: Shians  
> Created at: 2018-06-30 02:58:28 UTC  
> Url: https://github.com/boostorg/asio/issues/126#issuecomment-401512712  

Ok so from what I can tell, after the first join all the threads in the pool have stopped working. From the documentation:  
  
> This function blocks until the threads in the pool have completed.   
  
It was not clear to me that this also stops all the threads from taking on further jobs. It then makes sense for the jobs to be stacking up without ever being run.  
  
But as far as I can tell there's no way to restart the threads, if that's the case then an error should probably be thrown on a second join attempt as `std::thread` does.
