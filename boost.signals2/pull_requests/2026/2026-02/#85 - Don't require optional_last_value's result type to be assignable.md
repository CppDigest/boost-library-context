# #85 Don't require optional_last_value's result type to be assignable [Merged]

> Username: theuni  
> Created at: 2026-02-02 21:36:57 UTC  
> Updated at: 2026-02-06 21:43:26 UTC  
> Merged at: 2026-02-06 21:43:25 UTC  
> Closed at: 2026-02-06 21:43:25 UTC  
> Url: https://github.com/boostorg/signals2/pull/85  

As in title. A move-constructible return type should suffice for `optional_last_value`.  
  
Here's a minimal test-case demonstrating the issue:  
```c++  
#include <boost/signals2/signal.hpp>  
#include <cassert>  
  
struct moveonly_data  
{  
    moveonly_data(int data) : m_data(data){}  
    moveonly_data(moveonly_data&&) = default;  
  
    // The assignment operator should not be required here  
    moveonly_data& operator=(moveonly_data&&) = default;  
  
    moveonly_data(const moveonly_data&) = delete;  
    moveonly_data& operator=(const moveonly_data&) = delete;  
    int m_data;  
};  
  
moveonly_data moveonly_return_callback(int val)  
{  
    return {val+1};  
}  
  
int main()  
{  
    boost::signals2::signal<moveonly_data(int)> sig;  
    sig.connect(moveonly_return_callback);  
    int data{3};  
    auto ret = sig(data);  
    assert(ret->m_data == 4);  
}  
```  
  
With this fix applied, the move-assignment operator can be deleted as expected.

---
