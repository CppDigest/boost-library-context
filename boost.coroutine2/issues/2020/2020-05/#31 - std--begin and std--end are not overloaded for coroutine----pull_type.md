# #31 - std::begin and std::end are not overloaded for coroutine<>::pull_type [Closed]

> Username: robinjam  
> Created at: 2020-05-04 01:26:41 UTC  
> Updated at: 2021-08-11 09:24:31 UTC  
> Closed at: 2020-07-02 07:01:14 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/31  

[The documentation](https://www.boost.org/doc/libs/1_73_0/libs/coroutine2/doc/html/coroutine2/coroutine/asymmetric.html) says that `std::begin` and `std::end` are overloaded for `coroutine<>::pull_type`, but the following example code fails to compile on Clang (Xcode 11.4, macOS 10.15.4):  
  
```c++  
using namespace boost::coroutines2;  
  
auto my_coro = coroutine<int>::pull_type{  
    [](coroutine<int>::push_type& yield) {  
        for (int i = 0; i < 5; ++i) {  
            yield(i);  
        }  
    }  
};  
  
for (auto i = std::begin(my_coro); i != std::end(my_coro); ++i) {  
// ^ No matching function for call to 'begin', No matching function for call to 'end'  
    std::cout << *i << std::endl;  
}  
```  
  
Calling `detail::begin` and `detail::end` directly works, and range-based `for` also works.  
  
I'm not sure if this is a bug, a documentation problem, or a misunderstanding at my end.

---

## Comment 1

> Username: olk  
> Created at: 2020-07-02 07:01:08 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/31#issuecomment-652823384  

should be fixed with `f3bf547 provide std::begin/std::end overloads for pull_coroutine`
