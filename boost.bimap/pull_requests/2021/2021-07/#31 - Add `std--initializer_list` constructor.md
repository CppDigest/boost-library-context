# #31 Add `std::initializer_list` constructor [Open]

> Username: SuibianP  
> Created at: 2021-07-09 16:49:23 UTC  
> Updated at: 2021-07-09 23:53:55 UTC  
> Url: https://github.com/boostorg/bimap/pull/31  

C++11 allows list initialization for classes. STL map has been [added a constructor](https://isocpp.org/wiki/faq/cpp11-language#init-list) with the signature  
  
```c++  
map(initializer_list<value_type> il,  
    const allocator_type& alloc = allocator_type());  
```  
  
Added a similar constructor to bimap, allowing for initialization in the form of initializer list, such as  
  
```c++  
boost::bimap<double, long> tdn {  
    {114.514, 1919},  
    {891.931, 810}  
};  
```

---
