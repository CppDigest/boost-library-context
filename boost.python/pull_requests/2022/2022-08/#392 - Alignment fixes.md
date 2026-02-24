# #392 Alignment fixes [Merged]

> Username: dlaugt  
> Created at: 2022-08-29 08:44:12 UTC  
> Updated at: 2024-09-22 01:56:41 UTC  
> Merged at: 2024-09-22 01:56:41 UTC  
> Closed at: 2024-09-22 01:56:41 UTC  
> Url: https://github.com/boostorg/python/pull/392  

Fixed a crash on Solaris 11 with gcc 7.3.1 when a binding uses make_constructor () like:  
```cpp  
def_class<integer_type> ("numeric")  
  .def("__init__", make_constructor (&integer::from_tuple),  
       "Initialize from a tuple")  
```  
  
The crash is caused from pull request #360. It works fine when I revert the changes of this pull request.  
  
In make_constructor.hpp, the address of the structure pointer_holder should be aligned with the correct alignment value instead of the default value 1. Otherwise, it crashes in the placement new operator:  
```cpp  
(new (memory) holder(std::move(x)))  
```  
  
Fix the calculation of the padding required to align the start of a data structure. The padding cannot be equal to the alignment. It is always smaller than the alignment. The formula is taken from wikipedia which is equivalent to @aristk 's suggestion at #384:  
https://en.wikipedia.org/wiki/Data_structure_alignment

---
