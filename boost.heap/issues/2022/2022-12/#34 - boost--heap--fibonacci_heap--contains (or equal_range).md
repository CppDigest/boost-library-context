# #34 - boost::heap::fibonacci_heap::contains (or equal_range) [Closed]

> Username: vinipsmaker  
> Created at: 2022-12-10 14:27:01 UTC  
> Updated at: 2023-12-17 07:22:31 UTC  
> Closed at: 2023-12-17 07:22:30 UTC  
> Url: https://github.com/boostorg/heap/issues/34  

Would it be feasible to add `contains()` member function to `boost::heap::fibonacci_heap`? I'm using this structure to store shared pointers that are also tracked in different structures and sometimes I have to check whether a certain element has already been processed.

---

## Comment 1

> Username: timblechmann  
> Created at: 2023-12-17 07:22:30 UTC  
> Url: https://github.com/boostorg/heap/issues/34#issuecomment-1859060043  

not efficiently (i.e. not without an additional lookup table, which is out of the scope of this library)  
* consider using boost.intrusive's `treap`  
* use a separate lookup table  
* find the element in the `begin/end` range
