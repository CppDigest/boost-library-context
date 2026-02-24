# #42 - ambiguous call to begin end for coroutine<>::pull_type breaks range-based for (C++17 mode) [Closed]

> Username: hp-peti  
> Created at: 2021-08-10 14:45:07 UTC  
> Updated at: 2021-10-02 20:36:46 UTC  
> Closed at: 2021-10-02 20:36:46 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/42  

see #31  
  
the change introduced now doesn't allow   
  
```C++  
using namespace boost::coroutines2;  
  
auto my_coro = coroutine<int>::pull_type{  
    [](coroutine<int>::push_type& yield) {  
        for (int i = 0; i < 5; ++i) {  
            yield(i);  
        }  
    }  
};  
  
for (auto i : my_coro) {  
                 // ^ ambiguous call to 'begin', ambiguous call to 'end'  
    std::cout << i << std::endl;  
}  
```  
  
tested with gcc 10.3.0, using --std=c++17; and msvc also using c++17 mode  
  
using boost 1.76.0  
  
see also  #39
