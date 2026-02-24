# #32 - inserting into default-constructed flat_set creates null references [Closed]

> Username: sxeraverx  
> Created at: 2020-04-15 00:23:11 UTC  
> Updated at: 2020-10-20 09:35:38 UTC  
> Closed at: 2020-10-20 09:35:38 UTC  
> Url: https://github.com/boostorg/move/issues/32  

The start of the underlying vector of a default-constructed flat_set points to null.  
  
Merging into the flat_set involves converting the iterators to pointers as an optimization for contiguous containers (https://github.com/boostorg/container/blob/develop/include/boost/container/detail/flat_tree.hpp#L130)  
  
However, for a default-constructed flat-set, the pointer underlying `begin()` iterator points to null.  
  
The iterator-to-pointer conversion is done by taking the address of the result of dereferencing the pointer. (https://github.com/boostorg/move/blob/develop/include/boost/move/detail/iterator_to_raw_pointer.hpp#L36-L53)  
  
As an intermediate step, this ends up creating a null reference, which is undefined behavior.  
  
This should probably be fixed by comparing the iterator to a default-constructed iterator and returning nullptr if they compare equal, before attempting to dereference the iterator.

---

## Comment 1

> Username: sxeraverx  
> Created at: 2020-04-20 21:05:48 UTC  
> Url: https://github.com/boostorg/move/issues/32#issuecomment-616808701  

One more thought--using `iterator_to_raw_pointer` as an optimization for coming up with the end() pointer for a contiguous datastructure also seems broken because it dereferences a one-past-the-end pointer in the process.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2020-09-08 12:33:38 UTC  
> Url: https://github.com/boostorg/move/issues/32#issuecomment-688836230  

Revising the code, iterator_to_raw_pointer returns the internal pointer via i.operator->(); at least for boost::container::vector, which is the default container. Where is the null reference created?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2020-10-20 09:35:38 UTC  
> Url: https://github.com/boostorg/move/issues/32#issuecomment-712724846  

Closing as the report misses information and I can't reproduce it (maybe I'm not understanding something important?). Please reopen in case the problem persists.
