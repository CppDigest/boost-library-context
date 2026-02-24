# #124 - [Thread Safety] When data type is `std::shared_ptr`. it has undefined behaviour [Closed]

> Username: kingofknights  
> Created at: 2025-11-03 08:06:39 UTC  
> Updated at: 2025-11-03 14:34:09 UTC  
> Closed at: 2025-11-03 14:34:09 UTC  
> Url: https://github.com/boostorg/lockfree/issues/124  

Hi   
```  
#include <boost/lockfree/spsc_queue.hpp>  
#include <iostream>  
#include <memory>  
#include <thread>  
  
void fun(int i) { }  
  
int main() {  
    using TypeT = std::shared_ptr<int>;  
    using ConcurrentQueueT = boost::lockfree::spsc_queue<TypeT>;  
  
    ConcurrentQueueT queue(100);  
  
    std::jthread thread([&](const std::stop_token& token_) {  
        while (not token_.stop_requested()) {  
            queue.consume_all([](const TypeT& type_) { fun(*type_.get()); });  
        }  
    });  
  
    TypeT type = std::make_shared<int>(100);  
  
    while (true) {  
        std::this_thread::sleep_for(std::chrono::microseconds(100));  
        if (type.use_count() == 1) {  
            queue.push(type);  
        } else {  
            std::cout << "Count is " << type.use_count() << '\n';  
        }  
    }  
}  
```  
  
When complied with Intel-linux Compiler `icpx` with `boost-1.89`. after some thing my `Type::use_count` get two.  
  
what is this code doing.  
Push in queue where there is single copy of this object.  
  
In real life scenario. I have 10K shared_ptr which are getting push in this queue.   
I want to push only when it queue does not container the object already. which mean at any given time the use_count can be 2 only when it is stored in queue.  
  
but after some time queue is empty but use_count is still 2

---

## Comment 1

> Username: timblechmann  
> Created at: 2025-11-03 11:13:31 UTC  
> Url: https://github.com/boostorg/lockfree/issues/124#issuecomment-3480001368  

can you provide a better reproducer? the code above is fine: at the point where you check `type.use_count()`, there is no guarantee that the queue has already been emptied

---

## Comment 2

> Username: kingofknights  
> Created at: 2025-11-03 11:44:34 UTC  
> Url: https://github.com/boostorg/lockfree/issues/124#issuecomment-3480110230  

`use_count` can be   
`1` at the time of creation  
`2` when object is pushed in queue  
`1` when queue has done consume_all  
  
it can be `2` for a while if it is already in queue but   
what is happening after some time `assume million push `use_count` get stuck at `2`. and queue if empty
