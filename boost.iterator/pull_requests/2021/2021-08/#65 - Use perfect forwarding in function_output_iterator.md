# #65 Use perfect forwarding in function_output_iterator [Closed]

> Username: kkuchera  
> Created at: 2021-08-26 17:11:07 UTC  
> Updated at: 2021-08-26 19:34:51 UTC  
> Closed at: 2021-08-26 19:34:51 UTC  
> Url: https://github.com/boostorg/iterator/pull/65  

I wanted to use `function_output_iterator` with a `UnaryFunction` that was not copyable, only movable. Changing the constructor to use perfect forwarding resolves this.  
  
I tested this on a non-copyable `UnaryFunction` but didn't see any unit tests for this class where I could add them. What else should I do?

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-08-26 19:10:03 UTC  
> Url: https://github.com/boostorg/iterator/pull/65#issuecomment-906671317  

This is C++11 and Boost.Iterator must support C++03.

---
