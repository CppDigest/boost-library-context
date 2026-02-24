# #528 - Strange behavior of `hana::partial` [Open]

> Username: fdevinc  
> Created at: 2024-03-07 16:04:57 UTC  
> Updated at: 2024-03-07 16:04:57 UTC  
> Url: https://github.com/boostorg/hana/issues/528  

Hello, and thank you for the excellent library! I found a strange behavior using `hana::partial` as shown in the following snippet:  
```cpp  
auto lambda = [](int& i) {  
    std::cout << i++ << " ";  
    std::cout << i << std::endl;  
};  
const int j  = 0;  
auto partial = hana::partial(lambda, j);  
partial();  
```  
The above will print `0 1` and change the value of the constant variable `i`. Is this behavior expected? If I do not store the output of `hana::partial`, then the code does not compile as desired:  
```cpp  
auto lambda = [](int& i) {  
    std::cout << i++ << " ";  
    std::cout << i << std::endl;  
};  
const int j  = 0;  
hana::partial(lambda, j)();    // <= Error!  
```
