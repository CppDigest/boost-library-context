# #177 - Proposition to rename `unique` [Closed]

> Username: m-j-w  
> Created at: 2015-09-09 21:28:14 UTC  
> Updated at: 2015-09-09 23:04:30 UTC  
> Closed at: 2015-09-09 23:04:30 UTC  
> Url: https://github.com/boostorg/hana/issues/177  

Concerns https://github.com/ldionne/hana/blob/develop/include/boost/hana/unique.hpp, respectively https://github.com/ldionne/hana/blob/develop/include/boost/hana/fwd/unique.hpp:   
The method `unique` applied to a sequence is misleading. The default behaviour drops or removes duplicate consecutive elements from a sequence. Hence it ensures nonrecurrence, rather than ensuring uniqueness of elements over the whole sequence. In its more general form it drops elements when contiguous elements fulfil a predicate. However, `unique.by` should probably be well understood.   
  
Either way, both might be better denoted `filter_contiguous_by`, `filter_consecutive`, `remove_consecutive_if`, with a convenience function `remove_recurrences`. These names would probably provide a closer resemblence by name to `filter`, as in the monadic `filter`, or `remove` and `remove_if`. In resemblence to the STL equivalent `std::remove_if`, a name like `remove_consecutive_if` might also suit better. In any case, the name should reflect the binary operation between two consecutive/contiguous elements.  
  
A method `unique` should actually ensure uniqueness of all elements, however computational costly that may be...

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-09 21:39:06 UTC  
> Url: https://github.com/boostorg/hana/issues/177#issuecomment-139053522  

While I agree that `unique` suggests that it makes elements unique across the whole sequence, this name is modelled after `std::unique`. There's also `ranges::unique` in the Range-v3 proposal. I know the argument of _it was already named like this in library X_ is lame, but in the case of the standard it gives a stronger case.  
  
Of course, I'm not completely closed to the idea, but I don't like your IMO slightly verbose suggestions. Also, I would find it easier to change the name if the proposed name was already widely used in another language/community. Otherwise, nothing guarantees that other developers will find the other suggestion better than `unique`.

---

## Comment 2

> Username: m-j-w  
> Created at: 2015-09-09 22:57:18 UTC  
> Url: https://github.com/boostorg/hana/issues/177#issuecomment-139067102  

I see the dilemma, and must agree, since I didn't find a short expressive alternative for the general case. Working against such a strong standard doesn't seem too reasonable, although that appears to be C++ specific. The default could simply be `nonrecurrent`, provided a real `unique` method is available as well. Otherwise that differentiation is in that context indeed a non-issue.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-09-09 23:04:30 UTC  
> Url: https://github.com/boostorg/hana/issues/177#issuecomment-139068127  

While not ideal, the elements can be made unique by converting an arbitrary sequence to a `hana::set`. In general, if you need the guarantee of unique elements, you should use `hana::set`, but you lose the ability to fetch elements by indices.  
  
Like you said, this issue will arise again if we try to provide a real algorithm to make all the elements unique. I'll reopen this issue if we do.
