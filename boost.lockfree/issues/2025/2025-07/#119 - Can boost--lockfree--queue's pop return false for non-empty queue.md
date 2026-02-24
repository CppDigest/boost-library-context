# #119 - Can boost::lockfree::queue's pop return false for non-empty queue? [Open]

> Username: ara-ayvazyan  
> Created at: 2025-07-25 21:00:55 UTC  
> Updated at: 2025-07-25 21:00:55 UTC  
> Url: https://github.com/boostorg/lockfree/issues/119  

[Documentation](https://www.boost.org/doc/libs/1_85_0/doc/html/boost/lockfree/queue.html) states, that `pop` returns  
> true, if the pop operation is successful, **_false if queue was empty_**.  
  
However, the following example asserts sometimes (MSVC 19.42, even in debug) during `pop`:  
```cpp  
boost::lockfree::queue<int> queue{0u};  
const auto work = [&]  
{  
    while (true)  
    {  
        int x = 0;  
        BOOST_VERIFY(queue.push(x));  
        BOOST_VERIFY(queue.pop(x));  
    }  
};  
std::jthread t0{ work };  
std::jthread t1{ work };  
```  
The issue doesn't seem to happen when there is a barrier between the `push` and `pop` calls, suggesting that parallel `push` and `pop` calls may result in `pop` returning `false`, even when the initial queue isn't empty.
