# #114 - `buffered_channel::try_push` does not wake fibers in `pop` [Closed]

> Username: MacoChris  
> Created at: 2017-03-08 04:59:30 UTC  
> Updated at: 2017-03-08 07:13:41 UTC  
> Closed at: 2017-03-08 07:11:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/114  

Against boost 1.63 on amd64 linux  
  
Calls to `try_push` never cause any fibers already waiting on the channel to wake up.  
  
```cpp  
#include <cassert>  
#include <boost/fiber/all.hpp>  
  
int main() {  
    boost::fibers::buffered_channel<int> channel(16);  
  
    auto f = boost::fibers::async([&]{  
        channel.value_pop();  
    });  
  
    boost::this_fiber::sleep_for(std::chrono::seconds(1));  
  
    // Hangs in `f.get()`  
    auto push_result = channel.try_push(0);  
      
    // Completes  
    // auto push_result = channel.push(0);  
      
    // Completes with out waiting  
    // auto push_result = channel.push_wait_for(0, std::chrono::minutes(1));  
  
    assert(push_result == boost::fibers::channel_op_status::success);  
  
    f.get();  
}  
```  
  
I've noticed that `try_push` is in the class synopsis, but not explicitly documented as it was on the deprecated channel types. Is it supposed to have identical behaviour to `bounded_channel`?

---

## Comment 1

> Username: olk  
> Created at: 2017-03-08 07:13:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/114#issuecomment-284965526  

ty, buffered_channel::try_push() did not resume fibers waiting for enqueued items.  
fixed in branch develop.
