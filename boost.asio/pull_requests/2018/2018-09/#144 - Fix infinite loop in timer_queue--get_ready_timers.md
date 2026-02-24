# #144 Fix infinite loop in timer_queue::get_ready_timers [Closed]

> Username: elsid  
> Created at: 2018-09-13 18:26:48 UTC  
> Updated at: 2019-10-20 15:27:29 UTC  
> Closed at: 2019-05-25 12:06:51 UTC  
> Url: https://github.com/boostorg/asio/pull/144  

If cancelled timer has been moved then timer_queue::move_timer can  
rewrite other timer in the timer_queue::heap_. It leads to an  
infinite loop in `timer_queue::get_ready_timers`.  
  
This happens by following scenario:  
1. [timer_queue::cancel_timer](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/timer_queue.hpp#L176) calls [timer_queue::remove_timer](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/timer_queue.hpp#L191);  
  
Assume `canceled_timer_index` = `timer_queue::heap_.size() - 1`;  
  
2. [timer_queue::remove_timer](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/timer_queue.hpp#L262) calls [timer_queue::heap_.pop_back()](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/timer_queue.hpp#L275) (timer  
still `has heap_index_` = `canceled_timer_index`);  
3. [timer_queue::enqueue_timer](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/timer_queue.hpp#L80) calls [timer_queue::heap_.push_back()](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/timer_queue.hpp#L96) (new  
timer object is assigned to `canceled_timer_index` position);  
4. [timer_queue::move_timer](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/timer_queue.hpp#L197) [assing](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/timer_queue.hpp#L205) to `canceled_timer_index` position cancelled timer;  
  
After this heap is broken. Then following could happen;  
  
* `timer_queue::remove_timer` call for rewrited timer;  
* `timer_queue::enqueue_timer` call for timer already in the heap;  
* `timer_queue::get_ready_timers` gets into an infinite loop;  
  
For a cancelled timer with `heap_index_` = `std::numeric_limits<std::size_t>::max()` the [condition](https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/timer_queue.hpp#L204) will never be passed.

---

## Comment 1

> Username: elsid  
> Created_at: 2019-05-25 12:06:51 UTC  
> Url: https://github.com/boostorg/asio/pull/144#issuecomment-495913237  

Already fixed in master https://github.com/chriskohlhoff/asio/commit/ff28c6f7773ccd853b8f400eae412d2d4a4cd171

---
