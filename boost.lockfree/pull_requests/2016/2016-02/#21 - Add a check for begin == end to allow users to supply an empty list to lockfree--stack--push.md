# #21 Add a check for begin == end to allow users to supply an empty list to lockfree::stack::push [Open]

> Username: cdglove  
> Created at: 2016-02-19 03:58:36 UTC  
> Updated at: 2016-07-26 11:32:54 UTC  
> Url: https://github.com/boostorg/lockfree/pull/21  

I'm not 100% sure if this is desirable or not. A simple test is something like this;  
  
``` cpp  
#include <boost/lockfree/stack.hpp>  
  
int main()  
{  
    boost::lockfree::stack<int> s(10);  
    std::vector<int> v;  
    s.push(v.begin(), v.end());  
    return 0;  
}  
```  
  
This will crash (in debug at least), when dereferencing the begin iterator. However, it's possible this is as designed because it's expected that the user doesn't supply an empty list. I'm OK with either, but did hit this today.

---

## Comment 1

> Username: timblechmann  
> Created_at: 2016-06-11 09:24:13 UTC  
> Url: https://github.com/boostorg/lockfree/pull/21#issuecomment-225346948  

needs a test, will look into it

---

## Comment 2

> Username: cdglove  
> Created_at: 2016-06-11 16:05:43 UTC  
> Url: https://github.com/boostorg/lockfree/pull/21#issuecomment-225371859  

It's a question of if we want to impose the check on all callers even if they know for sure that their list is not empty. It might be a bit pessimistic to put the check inside push because then everyone pays for the cost of the branch. An ASSERT might also be acceptable.

---
