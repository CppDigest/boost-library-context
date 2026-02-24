# #359 - Possible data race on boost::detail::get_current_thread_data() [Closed]

> Username: sochsenk  
> Created at: 2021-10-06 10:00:02 UTC  
> Updated at: 2021-10-13 14:56:28 UTC  
> Closed at: 2021-10-13 14:55:20 UTC  
> Url: https://github.com/boostorg/thread/issues/359  

I'm using boost 1.70.0  
  
I'm not sure if this is a known issue or a false positive.  
I got helgrind issues (possible data races) reported when running the following code snippet:  
  
```  
   constexpr unsigned NR_THREADS = 3;  
   boost::barrier startBarrier(NR_THREADS);  
   boost::barrier endBarrier(NR_THREADS);  
  
   auto accumulate = [&]() {  
      unsigned sum = 0;  
      for (unsigned j = 0; j < 100; ++j) {  
           
         startBarrier.wait();  
           
         for (unsigned i = 0; i < 1000; ++i) {  
            sum += i;  
         }  
           
         endBarrier.wait();  
      }  
      return sum;  
   };  
     
   std::vector< boost::unique_future< unsigned > > results;  
   std::array< std::thread, NR_THREADS > threads;  
   for (unsigned t = 0; t < NR_THREADS; ++t) {  
     
      boost::packaged_task< unsigned > task{accumulate};  
      results.push_back(task.get_future());  
      threads[t] = std::thread(std::move(task));     
   }  
     
   for (auto&& r : results) {  
      std::cout << r.get() << "\n";  
   }  
     
   for (auto&& t : threads) {  
      t.join();  
   }  
```  
Note that I'm using std::thread but I got similar issues with boost::thread  
Besides other issues, it seems waiting for the future (r.get()) and the barrier waits, both seem to access boost::detail::get_current_thread_data().   
Part of the helgrind report:  
```  
==32289== Possible data race during read of size 4 at 0x6C5BBA8 by thread #1  
==32289== Locks held: 1, at address 0x8CFB630  
==32289==    at 0x6A52CCA: boost::thread_detail::enter_once_region(boost::once_flag&) (in ...)  
==32289==    by 0x6A47E8F: boost::detail::get_current_thread_data() (in /...)  
==32289==    by 0x4AE5CD: boost::condition_variable::wait(boost::unique_lock<boost::mutex>&) (in ...)  
==32289==    by 0x4AF0EA: boost::detail::shared_state_base::wait_internal(boost::unique_lock<boost::mutex>&, bool) (in ...)  
==32289==   
==32289== This conflicts with a previous write of size 4 by thread #2  
==32289== Locks held: 2, at addresses 0x6C5BC80 0x1FFEFFEEB0  
==32289==    at 0x6A52DA0: boost::thread_detail::commit_once_region(boost::once_flag&) (in ...)  
==32289==    by 0x6A47EB2: boost::detail::get_current_thread_data() (in ...)  
==32289==    by 0x4AE5CD: boost::condition_variable::wait(boost::unique_lock<boost::mutex>&) (in ...)  
==32289==    by 0x4AE82A: boost::barrier::wait() (in ...)  
```

---

## Comment 1

> Username: sochsenk  
> Created at: 2021-10-08 10:45:49 UTC  
> Url: https://github.com/boostorg/thread/issues/359#issuecomment-938541504  

I'm still analyzing, but my guess is there are boost::atomics somewhere below and Helgrind doesn't understand atomics.

---

## Comment 2

> Username: sochsenk  
> Created at: 2021-10-13 14:56:28 UTC  
> Url: https://github.com/boostorg/thread/issues/359#issuecomment-942390369  

I think it is a false positive due to Helgrinds inability to handle atomics.  
threadsanitizer couldn't find anything.
