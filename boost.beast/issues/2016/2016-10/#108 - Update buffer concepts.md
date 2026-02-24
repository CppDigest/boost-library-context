# #108 - Update buffer concepts: [Closed]

> Username: vinniefalco  
> Created at: 2016-10-03 16:59:07 UTC  
> Updated at: 2016-10-14 23:11:44 UTC  
> Closed at: 2016-10-14 23:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/108  

`is_DynamicBuffer` is missing checks for `size` and `max_size`.  
  
`is_BufferSequence` should have good iterator checks for `BidirectionalTraversalIterator` and `ReadableIterator`, see:  
http://www.boost.org/doc/libs/1_62_0/libs/iterator/doc/new-iter-concepts.html#readable-iterators-lib-readable-iterators

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-14 23:11:18 UTC  
> Updated at: 2016-10-14 23:11:26 UTC  
> Url: https://github.com/boostorg/beast/issues/108#issuecomment-253941015  

`size` and `max_size` checks are in **1.0.0-b16**. Its not clear that the other concepts can be fully developed into a trait.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-10-14 23:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/108#issuecomment-253941077  

Closing for now, we can revisit this when the Networking TS becomes standard, since it provides its own traits.
