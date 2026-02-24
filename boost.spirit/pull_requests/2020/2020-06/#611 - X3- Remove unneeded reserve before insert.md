# #611 X3: Remove unneeded reserve before insert [Merged]

> Username: Kojoley  
> Created at: 2020-06-10 00:04:28 UTC  
> Updated at: 2020-06-10 11:43:34 UTC  
> Merged at: 2020-06-10 11:43:32 UTC  
> Closed at: 2020-06-10 11:43:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/611  

Every sane container implementation will make preallocation if possible, calling `reserve` ourselves increases code size and creates a dead branch inside `insert` which cannot be removed by compiler.  
  
`vector<T>::insert` of major standard library implementations:  
- https://github.com/gcc-mirror/gcc/blob/2e0303d60a97ddfcdd8340184bb5dc45e0433327/libstdc%2B%2B-v3/include/bits/vector.tcc#L679  
- https://github.com/llvm/llvm-project/blob/fd3295fb6f981a5c030d7540b9eda67f9c723e0f/libcxx/include/vector#L1984  
- https://github.com/microsoft/STL/blob/7447ad59d61f50c13861878f340d051c298458df/stl/inc/vector#L911  
  
codegen comparison: https://godbolt.org/z/xJe3jr  
single allocation proof: https://godbolt.org/z/bXUyKG

---
