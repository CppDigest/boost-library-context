# #22 - Boost.Any allocator support RFC [Open]

> Username: apolukhin  
> Created at: 2021-04-24 16:02:50 UTC  
> Updated at: 2025-01-04 11:36:20 UTC  
> Url: https://github.com/boostorg/any/issues/22  

Right now it is possible to use Boost.Any with types that require special alignment (sse intrinsics, Eigen3 data types, ...) by overriding the global operators new and delete. This solution is far from optimal, since it results in everything in the whole program being overaligned.  
  
I'm opening this Request For Comments to discuss a possibly better solution to this problem.  
  
The gist of the idea is for Boost.Any to store a polymorphic allocator (of possibly zero size), such that the user can provide an allocator when initializing/assigning/copying an any object.  
  
This increases the API of Boost.Any (changes proposed below). It also decreases its performance, since allocating/deallocating memory would incur an extra virtual function call. It might also increase the size of the any object. One has, however, to consider the impact of the current workarounds in larger programs. Over-aligning all allocations might have a larger impact on performance and memory footprint than the proposed changes.  
  
I think the following interface should be enough:  
```  
    any(polymorphic_allocator allocator = default_allocator());   
  
    any(const any &, polymorphic_allocator allocator = default_allocator());   
  
    any(any &&, polymorphic_allocator allocator = default_allocator());   
  
    template<typename ValueType> any(const ValueType &, polymorphic_allocator allocator = default_allocator());   
  
    template<typename ValueType> any(ValueType &&, polymorphic_allocator allocator = default_allocator());   
```  
  
----  
Issue imported from https://svn.boost.org/trac10/ticket/11045  
Reported by: gonzalobg88@…
