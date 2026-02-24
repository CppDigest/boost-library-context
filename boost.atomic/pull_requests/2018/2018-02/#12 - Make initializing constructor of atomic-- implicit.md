# #12 Make initializing constructor of atomic<> implicit. [Merged]

> Username: Lastique  
> Created at: 2018-02-19 22:39:37 UTC  
> Updated at: 2018-02-20 07:39:43 UTC  
> Merged at: 2018-02-20 07:39:36 UTC  
> Closed at: 2018-02-20 07:39:36 UTC  
> Url: https://github.com/boostorg/atomic/pull/12  

This is an attempt to make `boost::atomic<>` interface closer to the standard. It  
makes a difference in C++17 as it mandates copy elision, which makes this code  
possible:  
  
```  
boost::atomic<int> a = 10;  
```  
  
It also makes `is_convertible<T, boost::atomic<T>>` return `true`, which has  
implications on the standard library components, such as std::pair.  
  
This removes the workaround for gcc 4.7, which complains that  
`operator=(value_arg_type)` is considered ambiguous with `operator=(atomic const&)`  
in assignment expressions, even though conversion to `atomic<>` is less preferred  
than conversion to `value_arg_type`. We try to work around the problem from the  
`operator=` side.  
  
Added a new compile test to check that the initializing constructor is implicit.

---
