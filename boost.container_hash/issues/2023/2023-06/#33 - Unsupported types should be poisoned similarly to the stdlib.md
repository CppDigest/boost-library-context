# #33 - Unsupported types should be poisoned similarly to the stdlib [Open]

> Username: cmazakas  
> Created at: 2023-06-21 15:45:06 UTC  
> Updated at: 2023-06-21 15:45:06 UTC  
> Url: https://github.com/boostorg/container_hash/issues/33  

Consider https://godbolt.org/z/5W1x94aTh  
  
We see that:  
```cpp  
static_assert(std::is_invocable_v<std::hash<rawr>, rawr>);  
```  
correctly fails until  
```cpp  
template<>  
struct std::hash<rawr> {  
  std::size_t operator()(rawr const&) const noexcept { return 0; }  
};  
```  
is defined.  
  
This would enable downstream consumers to write simple `is_hashable` trait checks.
