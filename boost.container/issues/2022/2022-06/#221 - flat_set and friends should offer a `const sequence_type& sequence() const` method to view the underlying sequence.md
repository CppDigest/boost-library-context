# #221 - flat_set and friends should offer a `const sequence_type& sequence() const` method to view the underlying sequence [Closed]

> Username: RedBeard0531  
> Created at: 2022-06-03 11:20:49 UTC  
> Updated at: 2022-06-25 18:29:24 UTC  
> Closed at: 2022-06-25 18:29:24 UTC  
> Url: https://github.com/boostorg/container/issues/221  

I have a use case where I need to expose a `const vector<T>&` accessor because the existing implementation just holds a plain `vector<T>`, but would also like to switch to a set-like type because the data is already guaranteed to be sorted. I was surprised that I couldn't just use `flat_set` for this because it does not offer a read-only view into the underlying sequence.  
  
It already is documented that the iterator type for `flat_set` must be the same as the underlying sequence, so its not like this is opening a new hole in the abstraction.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-06-25 18:28:11 UTC  
> Url: https://github.com/boostorg/container/issues/221#issuecomment-1166339639  

Thanks for the suggestion, it looks really useful and we already expose sequence_type through `extract_sequence` and `adopt_sequence`.
