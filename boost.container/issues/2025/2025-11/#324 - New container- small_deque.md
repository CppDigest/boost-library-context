# #324 - New container: small_deque [Open]

> Username: denzor200  
> Created at: 2025-11-21 20:07:28 UTC  
> Updated at: 2025-11-21 20:07:28 UTC  
> Url: https://github.com/boostorg/container/issues/324  

I propose the addition of `boost::container::small_deque` - a deque container that optimizes for small element counts by storing the first time appeared block in stack memory, similar to the existing `boost::container::small_vector` but maintaining deque semantics with efficient push/pop operations at both ends.  
  
The standard `std::deque` typically performs heap allocations even for small numbers of elements, which can be suboptimal for deque instances which contain few elements.  
Existing solutions like `boost::container::small_vector` provide stack optimization but lack efficient O(1) push/pop operations at both ends.  
  
```  
std::deque<int> d1 = {1, 2, 3};  // heap  
boost::container::small_deque<int, 4> d2 = {1, 2, 3};  // Only stack  
boost::container::small_deque<int, 4> d3 = {1, 2, 3, 4, 5};  // stack + heap (at least 20 bytes totaly used)  
boost::container::small_vector<int, 4> v1 = {1, 2, 3};  // Only stack  
boost::container::small_vector<int, 4> v2 = {1, 2, 3, 4, 5};  // Only heap (at least 36 bytes totally used)  
  
void process(const boost::container::small_deque<int, 8>& items) {  
    for (int item : items) {  // hot path cycle  
        process_item(item);  
    }  
}  
```
