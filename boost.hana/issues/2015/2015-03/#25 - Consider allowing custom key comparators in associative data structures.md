# #25 - Consider allowing custom key comparators in associative data structures [Open]

> Username: ldionne  
> Created at: 2015-03-22 16:41:28 UTC  
> Updated at: 2021-11-18 13:30:34 UTC  
> Url: https://github.com/boostorg/hana/issues/25  

Right now, a `Set` uses `equal` on its elements to determine their uniqueness. It might be useful to provide a custom key comparator for more complex behaviours. In the same vein, `Map` currently uses `equal` on its keys to determine the uniqueness of elements; it might be useful to allow for a custom comparator.  
  
However, compile-time sets are super hairy to implement, and honestly I am unsure how this could be achieved without ruining the compile-time performance of the associative data structures (which is already super poor because the implementation is naive). Also note that I do not think a compile-time efficient associative data structure can be implemented unless we put stringent requirements on how the elements are compared. Specifically, the only compile-time efficient associative data structure I can think of is where all the keys are types, or all the keys are indices (in which case it's a tuple).

---

## Comment 1

> Username: VaibhavMalik4187  
> Created at: 2021-11-18 13:30:33 UTC  
> Url: https://github.com/boostorg/hana/issues/25#issuecomment-972867629  

@ldionne I am interested in working on this issue, but I am a bit unclear about what am I supposed to do, if you can just help me understand a little about the issue, may be I can help
