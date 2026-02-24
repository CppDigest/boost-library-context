# #3 - Priority queue documentation has wrong complexities [Open]

> Username: jsonn  
> Created at: 2015-02-05 23:36:40 UTC  
> Updated at: 2015-02-05 23:36:40 UTC  
> Url: https://github.com/boostorg/heap/issues/3  

The complexity of a pairing heap is stated incorrectly. Amortised and actual complexity of push() should be $O(1)$. Amortised complexity of erase() and pop() should be $log(n)$.  
  
The implementation of the decrease() part of update() would normally be $2**(2sqrt(log(log(n)))$. I don't think the current implementation provides that as the check for empty children list is not conditional on the direction of cost change. As such, update() seems to be unconditionally $log(n)$ amortised, just like an increase() would be. Merge should be $O(1)$ as usual.
