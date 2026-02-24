# #36 fix uninitialized variable in default constructor of generator_iterator [Closed]

> Username: jeking3  
> Created at: 2017-11-04 14:21:32 UTC  
> Updated at: 2017-11-04 15:10:38 UTC  
> Closed at: 2017-11-04 15:10:33 UTC  
> Url: https://github.com/boostorg/iterator/pull/36  

This is an improvement that was sitting in Boost.Uuid seed_rng.hpp which I would like to move into the implementation proper.  By initializing the generator pointer, it ensures that "end" iterators are never valid nor do they match other iterators.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-11-04 15:03:43 UTC  
> Url: https://github.com/boostorg/iterator/pull/36#issuecomment-341903586  

The default-constructed `generator_iterator` is not the end iterator. In fact, IIUC, `generator_iterator` does not offer a way to have an end iterator. If you want one, you should use `function_input_iterator` instead.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-11-04 15:10:33 UTC  
> Url: https://github.com/boostorg/iterator/pull/36#issuecomment-341904093  

Interesting - that's how seed_rng.hpp uses it to pass it to a seed method that takes a beginning and an end iterator.  I'll close this.

---
