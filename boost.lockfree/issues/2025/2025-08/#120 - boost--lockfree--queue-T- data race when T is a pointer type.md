# #120 - [ Thread Safety ] boost::lockfree::queue<T> data race when T is a pointer type [Open]

> Username: summer-5  
> Created at: 2025-08-14 02:57:20 UTC  
> Updated at: 2025-11-03 07:59:44 UTC  
> Url: https://github.com/boostorg/lockfree/issues/120  

**Issue Summary**    
  
When using `boost::lockfree::queue<T>` with `T` as a pointer type (e.g., `PGconn*`), concurrent `pop()` operations may return the same front node to multiple threads, even if the return value is checked. This violates the queue's thread-safety guarantee.  
  
**Reproduction Steps**    
1. Define a queue with pointer type (e.g., `PGconn*` pointers): `boost::lockfree::queue<PGconn*> unsafe_queue(1024);`  
2. Concurrently call `push()` and `pop()` from multiple threads and track the number of times each `PGconn*` is popped and pushed.  
  
**Expected Behavior**  
  
Each enqueued `PGconn*` should be pushed and popped equal times.  
  
**Actual Behavior**  
  
For `boost::lockfree::queue<PGconn*>`, some `PGconn*` pointers are popped much more times than they are pushed.  
For `boost::lockfree::queue<Conn>` (where `Conn` wraps `PGconn*`), the behavior is correct (`pop count == push count`).  
  
**Example Code**  
```cpp  
// Thread-unsafe case  
boost::lockfree::queue<PGconn*> unsafe_queue(1024);  
// Concurrent pushes and pops will yield duplicate PGconn* pointers.  
  
// Thread-safe case  
struct Conn { PGconn* pgconn; };  
boost::lockfree::queue<Conn> safe_queue(1024);  
// Concurrent pushes and pops behave correctly.  
```  
**Root Cause Analysis**  
  
The issue likely stems from how pointer types are handled internally in `boost::lockfree::queue`. The queue may fail to atomically update the head node when `T` is a pointer, leading to race conditions. This does not occur when `T` is a user-defined type (e.g., `Conn`), possibly due to stricter memory ordering or type-specific handling.  
  
Every time an element is dequeued, its number of entries and exits is counted once. The number of pops is **always greater than or equal to** pushes. There has **never** been a situation where the number of pops is less than pushes. **Therefore, the problem is likely to lie in the** `pop()`.  
  
**Environment**  
  
`Boost version: 1.83.0`  
`Compiler: gcc/g++ 13.3`  
`OS: Linux x86_64`  
  
**Additional Notes**  
  
The bug may also affect other pointer types (`int*`, custom class pointers, etc.), but only `PGconn*` is explicitly tested.  
The lock-free context used for `push()` and `pop()` operations is confirmed to be correct (no external races).

---

## Comment 1

> Username: timblechmann  
> Created at: 2025-08-14 03:10:16 UTC  
> Url: https://github.com/boostorg/lockfree/issues/120#issuecomment-3186614671  

interesting. i don't recall any case where pointers are handled differently than other types. do you by any chance have a reduced test case?

---

## Comment 2

> Username: kingofknights  
> Created at: 2025-11-03 07:59:44 UTC  
> Url: https://github.com/boostorg/lockfree/issues/120#issuecomment-3479316299  

Same Thing is happening to me  
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
  
After some time all it print is use_count == 2.  
  
I am using 1.89 . Build using Intel-linux
