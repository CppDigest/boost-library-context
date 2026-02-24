# #256 - reproducible bug in spinlock_ttas [Closed]

> Username: dixlorenz  
> Created at: 2020-08-19 06:36:21 UTC  
> Updated at: 2024-08-28 10:57:13 UTC  
> Closed at: 2024-08-28 10:57:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/256  

I managed to reproduce the hang I got in #254 :  
  
```  
int main(int /*argc*/, const char * /*argv*/ [])  
{  
   struct Item {  
      int value{};  
      Item(int v = 0) : value(v) {}  
      Item(Item &&item)  
      {  
         boost::this_fiber::yield();   
         value = item.value;  
      }  
      Item &operator=(Item &&item)  
      {  
         boost::this_fiber::yield();  
         value = item.value;  
         return *this;  
      }  
   };  
     
   std::size_t channel_cap = 32;  
   std::size_t producer_count = std::thread::hardware_concurrency();  
   int max_value = 100;  
  
   std::vector<std::thread>              threads;  
   boost::fibers::buffered_channel<Item> channel(channel_cap);  
  
   boost::fibers::barrier b(producer_count + 1);  
  
   boost::fibers::fiber f_pop;  
  
   unsigned sum = 0;  
  
   for (unsigned i = 0; i < producer_count; ++i) {  
  
      threads.emplace_back([&, i] {  
         if (i == 0) {  
            f_pop = boost::fibers::fiber([&] {  
               {  
                  Item x{};  
                  while (boost::fibers::channel_op_status::success == channel.pop(x)) {  
                     sum += x.value;  
                  }  
               }  
            });  
         }  
         boost::fibers::fiber f([&] {  
            for (int x = 0; x < max_value; ++x)  
               channel.push(Item{ x });  
         });  
         f.join();  
         b.wait();  
         if (i == 0)  
            f_pop.join();  
      });  
   }  
  
   b.wait();  
   channel.close();  
  
   for (auto &t : threads)  
      t.join();  
        
   std::cout << sum << std::endl;  
  
```  
  
Obviously I am not really yielding the fiber in the move constructor/assignment, but when using buffered_channel::pop in a loop like this (which is perfectly reasonable) these get called; in my case the "previous" item returned an internal resource into a pool which was protected by a mutex which might yield the fiber and at that moment the channel is locked.  
  
I think this could be fixed by spinlock_ttas::lock yielding this_fiber and not this_thread; I can't test that, this_fiber is not available in spinlock_ttas.hpp, I don't know how to yield the current fiber with the operations available in that header.  
  
Technically it could probably also be solved in buffered_channel::pop(), but that would get really ugly.   
  
At the very least this needs a warning in the documentation.

---

## Comment 1

> Username: a952135763  
> Created at: 2021-10-07 10:14:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/256#issuecomment-937651990  

` Item &operator=(Item &&item)  
      {  
         boost::this_fiber::yield();  
         value = item.value;  
         return *this;  
      }`  
You overloaded the method.  
channel.push internal use std::move.  
internal spinlock_ttas is locked. After yielding, the internal lock is always locked.  
I think this is normal, just need some hint text.
