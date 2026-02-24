# #414 - `sync_bounded_queue::size` may return an incorrect value [Open]

> Username: belyaev-ms  
> Created at: 2025-11-24 14:19:02 UTC  
> Updated at: 2025-11-24 14:19:02 UTC  
> Url: https://github.com/boostorg/thread/issues/414  

## Bug Report: `sync_bounded_queue::size` may return an incorrect value  
  
### Description  
The `sync_bounded_queue::size` method can return an incorrect value under specific conditions.  
  
### Steps to Reproduce  
1. Create a `sync_bounded_queue` with a fixed capacity (e.g., 5).  
2. Fill the queue to its maximum capacity using `try_push`.  
3. Empty the queue completely using `try_pull`.  
4. Push a single element into the queue.  
  
After these steps, calling `size()` returns `0` instead of the expected `1`. However, the `empty()` method correctly returns `false`.  
  
### Minimal Reproducible Example  
```cpp  
#include <iostream>  
#include <boost/thread/concurrent_queues/sync_bounded_queue.hpp>  
int main() {  
    const size_t capacity = 5;  
    boost::sync_bounded_queue<int> queue(capacity);  
    size_t test_size = 0;  
    for (size_t i = 0; i < capacity; ++i) {  
        if (queue.try_push(i)) {  
            ++test_size;  
        }  
    }  
    for (size_t i = 0; i < capacity; ++i) {  
        if (queue.try_pull()) {  
            --test_size;  
        }  
    }  
    if (queue.try_push(1)) {  
        ++test_size;  
    }  
    std::cout << "size = " << queue.size() << "\ttest_size = " << test_size << std::endl;  
    std::cout << "empty = " << queue.empty() << "\ttest_empty = " << (test_size == 0) << std::endl;  
    std::cout << "[ TEST " << (queue.size() == test_size ? "PASSED" : "FAILED") << "]" << std::endl;  
    return 0;  
}  
```  
  
**Live Demo:** https://godbolt.org/z/hMTvTT6Kq  
  
### Actual Output  
```  
size = 0	test_size = 1  
empty = 0	test_empty = 0  
[ TEST FAILED]  
```  
  
### Expected Output  
```  
size = 1	test_size = 1  
empty = 0	test_empty = 0  
[ TEST PASSED]  
```  
  
### Proposed Fix  
The issue seems to be in the `size` method implementation. The current logic incorrectly handles the calculation when the queue has been wrapped around. The proposed fix simplifies the calculation:  
  
```diff  
--- a/include/boost/thread/concurrent_queues/sync_bounded_queue.hpp  
+++ b/include/boost/thread/concurrent_queues/sync_bounded_queue.hpp  
@@ -126,8 +126,7 @@ namespace concurrent  
     }  
     inline size_type size(lock_guard<mutex>& lk) const BOOST_NOEXCEPT  
     {  
-      if (full(lk)) return capacity(lk);  
-      return ((in_+capacity(lk)-out_) % capacity(lk));  
+      return ((in_+capacity_-out_) % capacity_);  
     }  
```  
  
### Validation  
A comprehensive test has been created to verify the fix works correctly across multiple operations:  
  
```cpp  
#include <iostream>  
#include <boost/thread/concurrent_queues/sync_bounded_queue.hpp>  
  
bool test_queue(boost::sync_bounded_queue<int>& queue, size_t& test_size, size_t count) {  
    for (size_t i = 0; i < 2 * queue.capacity(); ++i) {  
        for (size_t j = 0; j < count; ++j) {  
            ++test_size;  
            if (!queue.try_push(0) || queue.size() != test_size) {  
                std::cout << "[ TEST FAILED ]" << std::endl;  
                return false;  
            }  
        }  
        for (size_t j = 0; j < count; ++j) {  
            --test_size;  
            if (!queue.try_pull() || queue.size() != test_size) {  
                std::cout << "[ TEST FAILED ]" << std::endl;  
                return false;  
            }  
        }  
    }  
    return true;  
}  
  
int main() {  
    const size_t capacity = 5;  
    boost::sync_bounded_queue<int> queue(capacity);  
    size_t test_size = 0;  
    for (size_t i = 1; i <= capacity; ++i) {  
        if (!test_queue(queue, test_size, i)) {  
            return 1;  
        }  
    }  
    return 0;  
}  
```  
  
**Test with current implementation (fails):** https://godbolt.org/z/48nTe4zYP    
**Test with proposed fix (passes):** https://godbolt.org/z/33Ysr9hsE  
  
### Environment  
- Compiler: (any, reproduced on Godbolt with various compilers)  
- Platform: (any)  
  
[sync_bounded_queue.patch](https://github.com/user-attachments/files/23724828/sync_bounded_queue.patch)
