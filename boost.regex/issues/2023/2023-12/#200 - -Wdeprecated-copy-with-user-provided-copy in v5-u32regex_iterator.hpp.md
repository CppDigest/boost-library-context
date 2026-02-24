# #200 - -Wdeprecated-copy-with-user-provided-copy in v5/u32regex_iterator.hpp [Closed]

> Username: sapi33  
> Created at: 2023-12-05 13:31:21 UTC  
> Updated at: 2024-03-25 17:46:03 UTC  
> Closed at: 2024-03-25 17:46:02 UTC  
> Url: https://github.com/boostorg/regex/issues/200  

When method `u32regex_iterator& operator++()` of `u32regex_iterator` is used, then clang issues the following warning:  
  
> Boost/1.81.0/include/boost/regex/v5/u32regex_iterator.hpp:66:38: warning: definition of implicit copy constructor for 'u32regex_iterator_implementation<std::__wrap_iter<const char *>>' is deprecated because it has a user-provided copy assignment operator [-Wdeprecated-copy-with-user-provided-copy]  
   66 |    u32regex_iterator_implementation& operator=(const u32regex_iterator_implementation&);  
  
When comparing with `regex_iterator` one can see that the corresponding copy constructor `u32regex_iterator_implementation(const u32regex_iterator_implementation& other)` in class `u32regex_iterator_implementation` is not present, i.e. it is implicit but should be explicit as the user-provided copy assignment operator `u32regex_iterator_implementation& operator=(const u32regex_iterator_implementation&)` is declared.  
  
A fix should be to add the copy constructor to class `u32regex_iterator_implementation` explicitly:  
```c++  
u32regex_iterator_implementation(const u32regex_iterator_implementation& other)  
    :what(other.what), base(other.base), end(other.end), re(other.re), flags(other.flags){}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-03-25 17:46:02 UTC  
> Url: https://github.com/boostorg/regex/issues/200#issuecomment-2018561607  

Duplicate issue, fixed in develop.  Many thanks for submitting.
