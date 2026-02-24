# #85 - dynamic_bitset move CTOR and operator should be noexcept if C++ standard is 17 or higher [Closed]

> Username: d3matt  
> Created at: 2025-01-28 16:47:24 UTC  
> Updated at: 2025-08-05 09:11:38 UTC  
> Closed at: 2025-08-05 09:11:38 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/85  

The underlying std::vector's move CTOR and operator became noexcept with C++ 17. boost::dynamic_bitset should be able to follow suit in that case

---

## Comment 1

> Username: d3matt  
> Created at: 2025-01-28 16:56:14 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/85#issuecomment-2619561458  

I'm pretty sure the noexcept specifier should match the one from std::vector. Something like:  
```  
noexcept(std::allocator_traits<Allocator>::propagate_on_container_move_assignment::value  
|| std::allocator_traits<Allocator>::is_always_equal::value)  
```
