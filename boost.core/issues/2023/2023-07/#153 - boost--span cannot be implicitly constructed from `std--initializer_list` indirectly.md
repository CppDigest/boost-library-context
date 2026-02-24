# #153 - boost::span cannot be implicitly constructed from `std::initializer_list` indirectly [feature-request] [Open]

> Username: hadrielk  
> Created at: 2023-07-23 16:25:10 UTC  
> Updated at: 2023-07-23 16:25:10 UTC  
> Url: https://github.com/boostorg/core/issues/153  

NOTE: This isn't something `std::span` in C++20 supports either, unfortunately. But since Boost tries to offer things a future standard might adopt, I think this fits?  
  
Because `boost::span` has no constructor signature with a direct `std::initializer_list` argument, but only a templated "range" one, this works:  
```cpp  
std::initializer_list<int> il{1,2,3,4};  
boost::span<int> sp(il);  
```  
  
but this does not, because it requires two implicit conversions to occur:  
```cpp  
boost::span<int> sp({1,2,3,4});  
```  
  
I believe the above would work if this were to be added:  
```cpp  
    constexpr span(std::initializer_list<T> il) noexcept  
        : s_(boost::data(il), il.size())  
    {}  
```  
  
I can provide a PR, if you wish.  
  
I recognize this can lead to lifetime issues for the backing array, but that's already true for `span` regardless.  
  
There is a std proposal for this, I believe: [p2447r2](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2447r2.html), but I don't know its disposition.
