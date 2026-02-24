# #84 - Consider overloading slice instead of having subsequence [Closed]

> Username: ldionne  
> Created at: 2015-06-05 21:56:16 UTC  
> Updated at: 2015-09-08 22:32:06 UTC  
> Closed at: 2015-09-08 22:32:06 UTC  
> Url: https://github.com/boostorg/hana/issues/84  

We could have a `slice(xs, indices)` overload instead of a separate method `subsequence`.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-05 15:15:46 UTC  
> Url: https://github.com/boostorg/hana/issues/84#issuecomment-137963952  

Gotcha: `slice` can move from all the elements because it knows that there won't be double-moves, whereas `subsequence` must take for account the case where there are duplicate indices (that would cause us to double-move).

---

## Comment 2

> Username: m-j-w  
> Created at: 2015-09-08 19:32:32 UTC  
> Url: https://github.com/boostorg/hana/issues/84#issuecomment-138677614  

That may be useful, in particular since the current `slice(xs, from, to)` could be replaced by `slice(xs, make_range(from, to)` and thus would only require a convenience helper function. And that would permit some other nice optimisations, since the range would never be required in its expanded form, since it models `Iterable`, which again would allow to perform intersections and unions of the range(s) to select the slice, or even quite a lot more complex element selection schemes.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-09-08 20:15:43 UTC  
> Url: https://github.com/boostorg/hana/issues/84#issuecomment-138687056  

Perhaps I don't understand your comment properly, but these other nice optimizations could be implemented for the current `subsequence` method it seems, couldn't they?  
  
The main thing that is preventing me from going forward with this is the inability to move the values from the source sequence into the destination sequence when we don't know that there are no duplicate indices. `slice`, by allowing only an interval to be specified, gives us that guarantee and allows us this (runtime) optimization.  
  
Also, while we could technically check the indices at compile-time and move only from the indices that are not duplicated, I am 90% sure this will be too costly at compile-time to be worth it.  
  
In all cases, I think having an overload of `slice(xs, indices)` which performs the current optimization when the indices are a `hana::range` is definitely possible, and I don't know why I haven't thought about it before.
