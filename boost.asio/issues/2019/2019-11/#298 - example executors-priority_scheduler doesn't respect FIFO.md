# #298 - example executors/priority_scheduler doesn't respect FIFO [Closed]

> Username: bansan85  
> Created at: 2019-11-06 07:59:34 UTC  
> Updated at: 2020-12-30 01:11:04 UTC  
> Closed at: 2020-12-30 01:11:03 UTC  
> Url: https://github.com/boostorg/asio/issues/298  

I played with priority_scheduler and I found that priority_queue doesn't respect FIFO.  
  
With the example in boost source, there's no problem. But with :  
  
```  
   auto high = sched.get_executor(2);  
   dispatch(med, [] { std::cout << "2\n"; });  
   dispatch(low, [] { std::cout << "1\n"; });  
   dispatch(med, [] { std::cout << "22\n"; });  
   dispatch(low, [] { std::cout << "11\n"; });  
   dispatch(high, [] { std::cout << "3\n"; });  
   dispatch(high, [] { std::cout << "33\n"; });  
   dispatch(high, [] { std::cout << "333\n"; });  
   dispatch(sched.get_executor(-1), [&] { sched.stop(); });  
```  
  
The output with Visual Studio V2017, I have :  
  
```  
3  
33  
333  
22  
2  
11  
1  
```  
  
``2`` should be before ``22`` and ``1`` before ``11``.  
  
Actually, to solve this problem, I had a timestamp in ``item_base`` and I'm using it in the comparator function ``struct item_comp``  
  
Is Visual Studio wrong ? Is there a problem with boost ? How can I fix this problem without adding a timestamp ?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:11:02 UTC  
> Url: https://github.com/boostorg/asio/issues/298#issuecomment-752293170  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#730](https://github.com/chriskohlhoff/asio/issues/730).
