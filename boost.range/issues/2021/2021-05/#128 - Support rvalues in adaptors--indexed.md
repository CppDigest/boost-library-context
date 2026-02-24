# #128 - Support rvalues in adaptors::indexed [Open]

> Username: FelixDombek  
> Created at: 2021-05-18 09:06:40 UTC  
> Updated at: 2021-05-18 09:06:40 UTC  
> Url: https://github.com/boostorg/range/issues/128  

Using a normal ranged for with an rvalue is supported in C++, so adaptors::indexed should support it too.   
  
Currently it is too easy to get a segfault:  
  
```  
std::vector<int> getVec();  
for (const auto& i : boost::adaptors::index(getVec())) { std::cout << i; }  
```  
  
This should have a move constructor and be able to store the temporary object.
