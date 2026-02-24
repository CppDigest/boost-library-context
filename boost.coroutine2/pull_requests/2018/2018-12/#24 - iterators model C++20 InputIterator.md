# #24 [pull_coroutine] iterators model C++20 InputIterator [Merged]

> Username: CaseyCarter  
> Created at: 2018-12-05 17:27:25 UTC  
> Updated at: 2018-12-05 18:47:50 UTC  
> Merged at: 2018-12-05 18:13:58 UTC  
> Closed at: 2018-12-05 18:13:58 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/24  

The C++20 `InputIterator` concept requires post-increment indirectly via the [`WeaklyIncrementable`](http://eel.is/c++draft/iterator.concept.winc) concept, but doesn't constrain the return type unlike the "old [input iterator requirements](http://eel.is/c++draft/input.iterators). Consequently the iterators of coroutine generators - such as `pull_coroutine` - can model `InputIterator`.  
  
Drive-by: Remove explicit implementations of copy construction/assignment that do what the default implementations would; don't pessimize by avoiding self-swap of pointers.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2018-12-05 18:01:37 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/24#issuecomment-444582585  

The CI failure looks unrelated.

---

## Comment 2

> Username: CaseyCarter  
> Created_at: 2018-12-05 18:07:12 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/24#issuecomment-444584419  

> The CI failure looks unrelated.  
  
Yes, `is_function` is trying to deduce a bool to the `noexcept` specifier of the function type which isn't standard C++.

---

## Comment 3

> Username: olk  
> Created_at: 2018-12-05 18:13:54 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/24#issuecomment-444586598  

ty

---
