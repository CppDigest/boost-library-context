# #314 - deadline timer cause infinteloop [Closed]

> Username: jaycelq  
> Created at: 2019-12-28 05:30:14 UTC  
> Updated at: 2024-12-11 10:18:34 UTC  
> Closed at: 2019-12-30 12:37:06 UTC  
> Url: https://github.com/boostorg/asio/issues/314  

https://github.com/chriskohlhoff/asio/issues/448#issue-543066986  
  
I use deadline timer in my project, and it will cause infinite loop occasionally in function get_ready_times.  
  
I guess the heap or the list is corrupted. So I add some log and assert in timer_queue.hpp  
```  
  void hpPrintHeapInfo()  
  {  
      for (std::size_t i = 0; i < heap_.size(); ++i)  
      {  
          per_timer_data* timer = heap_[i].timer_;  
          std::cout << "thread: " << syscall(SYS_gettid) << " heap[" << i << "].timer_->heap_index_: " << timer->heap_index_  
                    << " time: " << Time_Traits::to_posix_duration(Time_Traits::subtract(heap_[i].time_, Time_Traits::now())).total_milliseconds()   
                    << " per_timer_data addr:" << timer << std::endl;  
      }  
      for (std::size_t i = 0; i < heap_.size(); ++i)  
      {  
          per_timer_data* timer = heap_[i].timer_;  
          assert(timer->heap_index_ == i);  
      }  
      std::cout << "thread: " << syscall(SYS_gettid) << "hpPrintHeapInfo over" << std::endl;  
  }  
  
  void hpPrintListInfo()  
  {  
      for (per_timer_data* p = timers_; p; p = p->next_)  
      {  
          std::cout << "thread: " << syscall(SYS_gettid) << " addr: " << p << " heap_index: " << p->heap_index_ << std::endl;  
      }  
  }  
  
...  
  // Remove a timer from the heap and list of timers.  
  void remove_timer(per_timer_data& timer)  
  {  
    // Remove the timer from the heap.  
    std::size_t index = timer.heap_index_;  
    std::cout << "thread: " << syscall(SYS_gettid) << " remove_timer index: " << index << " addr: " << &timer << std::endl;  
    if (!heap_.empty() && index < heap_.size())  
    {  
      if (index == heap_.size() - 1)  
      {  
        timer.heap_index_ = (std::numeric_limits<std::size_t>::max)();  
        heap_.pop_back();  
      }  
      else  
      {  
        swap_heap(index, heap_.size() - 1);  
        timer.heap_index_ = (std::numeric_limits<std::size_t>::max)();  
        heap_.pop_back();  
        if (index > 0 && Time_Traits::less_than(  
              heap_[index].time_, heap_[(index - 1) / 2].time_))  
          up_heap(index);  
        else  
          down_heap(index);  
      }  
    }  
  
    // Remove the timer from the linked list of active timers.  
    if (timers_ == &timer)  
      timers_ = timer.next_;  
    if (timer.prev_)  
      timer.prev_->next_ = timer.next_;  
    if (timer.next_)  
      timer.next_->prev_= timer.prev_;  
    timer.next_ = 0;  
    timer.prev_ = 0;  
      
    hpPrintListInfo();  
    hpPrintHeapInfo();  
  }  
```  
  
And I get the log when the assertion is not satisfied. It seems the heap is corrupted unexpectedly and the list lost the last element, but I cannot figure out why this happened. I think this is a fatal problem, can any one help me with that issue? @chriskohlhoff   
```  
thread: 12965 enqueue_timer heap_index_: 5 addr:0x61500001ffd8  
thread: 12965 addr: 0x61500001ffd8 heap_index: 5  
thread: 12965 addr: 0x615000022558 heap_index: 4  
thread: 12965 addr: 0x615000024858 heap_index: 3  
thread: 12965 addr: 0x7f50bc8f18e8 heap_index: 0  
thread: 12965 addr: 0x6150000204d8 heap_index: 1  
thread: 12965 addr: 0x615000024358 heap_index: 2  
thread: 12965 heap[0].timer_->heap_index_: 0 time: 514 per_timer_data addr:0x7f50bc8f18e8  
thread: 12965 heap[1].timer_->heap_index_: 1 time: 7996 per_timer_data addr:0x6150000204d8  
thread: 12965 heap[2].timer_->heap_index_: 2 time: 7996 per_timer_data addr:0x615000024358  
thread: 12965 heap[3].timer_->heap_index_: 3 time: 9799 per_timer_data addr:0x615000024858  
thread: 12965 heap[4].timer_->heap_index_: 4 time: 9999 per_timer_data addr:0x615000022558  
thread: 12965 heap[5].timer_->heap_index_: 5 time: 9999 per_timer_data addr:0x61500001ffd8  
thread: 12965hpPrintHeapInfo over  
thread: 12965 get_ready_timers begin, heap size: 6  
thread: 12965 remove_timer index: 0 addr: 0x7f50bc8f18e8  
thread: 12965 addr: 0x61500001ffd8 heap_index: 2  
thread: 12965 addr: 0x615000022558 heap_index: 4  
thread: 12965 addr: 0x615000024858 heap_index: 3  
thread: 12965 addr: 0x6150000204d8 heap_index: 18446744073709551615  
thread: 12965 heap[0].timer_->heap_index_: 0 time: 7464 per_timer_data addr:0x615000024358  
thread: 12965 heap[1].timer_->heap_index_: 18446744073709551615 time: 7464 per_timer_data addr:0x6150000204d8  
thread: 12965 heap[2].timer_->heap_index_: 2 time: 9468 per_timer_data addr:0x61500001ffd8  
thread: 12965 heap[3].timer_->heap_index_: 3 time: 9267 per_timer_data addr:0x615000024858  
thread: 12965 heap[4].timer_->heap_index_: 4 time: 9467 per_timer_data addr:0x615000022558  
nodeserver: /home/data/jenkins/jenkins_server_code/workspace/hk4e_trunk/../hk4e_ext/boost/../boost_1_70_0d/boost/asio/detail/timer_queue.hpp:93: void boost::asio::detail::timer_queue<Time_Traits>::hpPrintHeapInfo() [with Time_Traits = boost::asio::detail::forwarding_posix_time_traits]: Assertion `timer->heap_index_ == i' failed.  
```
