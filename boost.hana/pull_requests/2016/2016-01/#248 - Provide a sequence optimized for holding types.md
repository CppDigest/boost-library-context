# #248 Provide a sequence optimized for holding types [Merged]

> Username: ldionne  
> Created at: 2016-01-30 15:25:38 UTC  
> Updated at: 2016-02-15 21:56:34 UTC  
> Merged at: 2016-02-15 21:54:46 UTC  
> Closed at: 2016-02-15 21:54:46 UTC  
> Url: https://github.com/boostorg/hana/pull/248  

In its current form, Hana cannot easily compete (in terms of compile-time performance) against pure type-level metaprogramming libraries that provide sequences holding types only. This is because `hana::tuple` does so much more than holding types only, and the compiler just has more work to do.  
  
I think the remedy to this is to provide a sequence optimized for holding types only. This way, one would retain the expressive power of Hana, while providing compile-times that are very close to pure type-level sequences. This pull request explores such a sequence.

---
