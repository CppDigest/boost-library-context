# #152 - boost::span does not have constructors with iterators [Closed]

> Username: hadrielk  
> Created at: 2023-07-23 16:02:16 UTC  
> Updated at: 2023-07-23 16:27:26 UTC  
> Closed at: 2023-07-23 16:20:31 UTC  
> Url: https://github.com/boostorg/core/issues/152  

In C++20 `std::span` supports iterator arguments for a couple of its constructor signatures. `boost::span` does not - only raw pointers.  
  
That means one cannot do this:  
```cpp  
std::vector<int> vec{1,2,3,4};  
  
boost::span<int> sp1(vec.begin(), vec.end()); // error  
boost::span<int> sp2(vec.begin(), 2); // error  
```  
  
I can provide a PR, if you wish.

---

## Comment 1

> Username: glenfe  
> Created at: 2023-07-23 16:19:36 UTC  
> Url: https://github.com/boostorg/core/issues/152#issuecomment-1646880673  

See https://github.com/boostorg/core/issues/119

---

## Comment 2

> Username: hadrielk  
> Created at: 2023-07-23 16:27:25 UTC  
> Url: https://github.com/boostorg/core/issues/152#issuecomment-1646882654  

Oops, I should have looked at the close issues - thanks @glenfe.
