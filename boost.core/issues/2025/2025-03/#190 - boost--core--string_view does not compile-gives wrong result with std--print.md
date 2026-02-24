# #190 - boost::core::string_view does not compile/gives wrong result with std::print [Closed]

> Username: libbooze  
> Created at: 2025-03-05 15:45:11 UTC  
> Updated at: 2025-06-25 15:11:02 UTC  
> Closed at: 2025-06-25 15:11:02 UTC  
> Url: https://github.com/boostorg/core/issues/190  

It is quite possible this is just bug in `std::print `implementations, but if you can make something to make it work it would be nice.  
  
For gcc14.2 simple use of `std::print `does not compile, for clang 19.1(with libc++) things are even worse, code compiles, but `string_view` is not printed as string, but as a container, i.e. this code  
```  
int main() {  
    boost::core::string_view bsv{"abc"};  
    std::string_view ssv{"abc"};  
    std::println("{}", bsv);  
    std::println("{}", ssv);  
}  
```  
  
outputs  
```  
['a', 'b', 'c']  
abc  
```  
  
https://godbolt.org/z/e7fva7Y7c

---

## Comment 1

> Username: octopus-prime  
> Created at: 2025-06-15 08:12:07 UTC  
> Url: https://github.com/boostorg/core/issues/190#issuecomment-2973580088  

Until fix this does help  
  
`std::println("{}", (std::string_view) bsv);`
