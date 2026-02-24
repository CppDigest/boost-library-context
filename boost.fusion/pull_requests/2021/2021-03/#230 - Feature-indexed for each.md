# #230 Feature/indexed for each [Open]

> Username: denzor200  
> Created at: 2021-03-06 20:38:52 UTC  
> Updated at: 2021-03-06 20:38:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/230  

This patch extends `for_each` algorithm, implements a small and useful С++11 functional. For example:  
```  
typedef vector<int, char, double, char const*> vector_type;  
vector_type v(1, 'x', 3.3, "Ruby");  
for_each(v, [](const auto& v, int i){  
    std::cout << "[ " << i <<  " : " << v << " ] ";  
});  
std::cout << std::endl;  
```  
The index value is also available at compile time

---
