# #24 - Comparisons with execution_contexts. [Closed]

> Username: NicolBolas  
> Created at: 2015-10-15 18:45:38 UTC  
> Updated at: 2015-12-26 14:44:08 UTC  
> Closed at: 2015-12-26 14:44:07 UTC  
> Url: https://github.com/boostorg/context/issues/24  

`execution_context` is a very low-level, low-overhead tool. As it should be; it needs to be a foundation for others to build on top of. However, it does lack a few features that make working with it more difficult than strictly necessary.  
  
For example, imagine a scheduler where users can pass it an `execution_context`. This scheduler will execute these contexts (within the confines of a single thread, perhaps). The purpose of the system is to allow each coroutine to decide when it is OK to task-switch to another task in the scheduler. So you effectively yield to the scheduler, who picks the next task to execute (note that "yield" does not necessarily mean switching to the scheduler's context, as that would be a waste of a context switch).  
  
That is all implementable as Boost.Context currently exists. The problem is how to prevent mistakes.  
  
If a user hands an `execution_context` off to the scheduler to manage, how do you prevent the user from accidentally adding the same context twice? The traditional way would be with `set` (or more reasonably `flat_set`). But without operator<, and without anything that could be used to create such an comparison operation (comparing the internal data, not the wrapper), such containers cannot be used.  
  
Also, with such a system, it is difficult to remove a context from its care. It is not impossible of course; one can use a helper object system like Boost.Signal2 does to unregister listeners. But it would be easier for users if they could just say `unregister(ec)`.  
  
There are other situations where these operations would be useful. For example, users might want to associate arbitrary user data with an execution context. This would allow them to get `execution_context::current()` and use that to fetch that context's additional data (let's ignore the fact that this would keep the context alive longer than is strictly necessary, since intrusive_ptr's don't have weak references. That's a possible feature for another day).  
  
The common way to do this would be with a map of some kind, between `execution_context` as the key and the associated data values. `std::map` needs `operator<` or some other form of comparability between key types. `std::unordered_map` needs both `operator==` and hashing support.  
  
What's worse is that there is no way to synthesize such operations (at least, not without trickery/UB). `execution_context` is a fully opaque wrapper around a smart pointer, with no way to tell anything about the internal data except whether it actually wraps some internal data.  
  
Given the current implementation of Boost.Context as a wrapper around a reference-counted pointer to a type, it should be trivial to implement `operator==`, `operator<`, and hashing support. These features would have no impact on the performance of the type, and they would non-trivially improve its usability.

---

## Comment 1

> Username: olk  
> Created at: 2015-12-26 14:44:07 UTC  
> Url: https://github.com/boostorg/context/issues/24#issuecomment-167328897  

done
