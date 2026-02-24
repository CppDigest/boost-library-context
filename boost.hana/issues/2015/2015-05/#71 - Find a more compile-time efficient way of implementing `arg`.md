# #71 - [Functional] Find a more compile-time efficient way of implementing `arg` [Closed]

> Username: ldionne  
> Created at: 2015-05-04 18:01:16 UTC  
> Updated at: 2015-07-04 22:10:31 UTC  
> Closed at: 2015-07-04 22:10:31 UTC  
> Url: https://github.com/boostorg/hana/issues/71  

The current implementation is less compile-time efficient than `variadic::at`, but it allows perfect forwarding. Is there a way to get the efficiency of `variadic::at` with perfect forwarding?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-07-04 22:10:31 UTC  
> Url: https://github.com/boostorg/hana/issues/71#issuecomment-118557817  

Actually, I think that's about as good as can be done without creating  something that's equivalent to a tuple.
