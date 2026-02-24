# #125 WIP: Fixes #122 [Open]

> Username: mkatliar  
> Created at: 2018-10-23 11:12:34 UTC  
> Updated at: 2018-10-27 04:23:58 UTC  
> Url: https://github.com/boostorg/graph/pull/125  

I have changed the `vertices()`, `edges()`, `adjacent_vertices()`, `in_edges()`, `out_edges()` functions such that they return an object which inherits `std::pair<Iterator, Iterator>`, and additionally models a container. This allows the use of range-based for loops with the results of those functions, and keeps compatibility with the existing code.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2018-10-23 12:44:32 UTC  
> Url: https://github.com/boostorg/graph/pull/125#issuecomment-432231053  

It's great that you have maintained backward compatibility, but changing those interfaces is not something to be done lightly. This looks like a lot of code for marginal benefit because, as I mentioned in the issue tracker, one can simply use `boost::for_each` (or any other algorithm) from Boost.Range.  
Also, it's adding what looks like a non-trivial amount of type computation to what was previously a very simple `std::pair`. I try not to care about compilation time in general, but I guess when it's something that I don't need, then I care.  :)  
  
Another way to solve this problem would be to have a `boost::pair` equivalent data structure to `std::pair`, because then the simple solution would be to add overloads of `begin`/`end` functions to the boost namespace for `pair` of iterators:  
```C++  
namespace boost {  
  template <typename T>  
  T begin(pair<T, T> const &iterators) {  
    return iterators.first;  
  }  
  
  template <typename T>  
  T end(pair<T, T> const &iterators) {  
    return iterators.second;  
  }  
}  
```  
You could also add a bunch of interface checking with `enable_if` to ensure that `T` is an iterator, etc.  
There's a lot of work there to add a boost::pair, but the final result would be more elegant and maintain the low overhead that the current implementation has.

---

## Comment 2

> Username: mkatliar  
> Created_at: 2018-10-23 16:21:59 UTC  
> Url: https://github.com/boostorg/graph/pull/125#issuecomment-432318183  

@jeremy-murphy I like the solution with `boost::pair`, it is indeed much simpler. `boost::pair` is something that does not exist yes, does it?

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2018-10-26 08:44:03 UTC  
> Url: https://github.com/boostorg/graph/pull/125#issuecomment-433334388  

I guess we could add a `pair` class in some private Boost.Graph namespace that simply inherits from `std::pair` and put those `begin`/`end` functions in that namespace.  
One day there might be call for a general-purpose pair class in Boost but for now all we want is `std::pair` in a namespace that we control.  
@jzmaddock does that sound reasonable?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-10-26 17:38:18 UTC  
> Url: https://github.com/boostorg/graph/pull/125#issuecomment-433486892  

I confess I'm somewhat wary of this as it's a breaking interface change: I admit there probably isn't much code it would break, but I'm sure it would break some.  
  
While this is nice to have, using:  
  
```  
using boost::make_Iterator_range;  
  
for ( auto edge: make_iterator_range(my_graph.edges()))  
{  
   // do something.  
}  
```  
  
Isn't that much harder?  
  
I do see the attraction though and changing pair to class_derived_from_pair isn't such a big change, I guess the question is whether a breaking change like this is something that the CMT should be doing?  Maybe raise it on the mailing list?

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2018-10-27 04:23:58 UTC  
> Url: https://github.com/boostorg/graph/pull/125#issuecomment-433589550  

Yeah, I made the same suggestion on the issue that this PR pertains to, plus the suggestion of using `boost::for_each`.  
I personally would consider this a fairly low priority enhancement given the relatively easy workarounds.  
As for breaking existing code, I'm a little less sympathetic.  :) By which I just mean that I don't think clients should be depending on this pair type being `std::pair` but instead just on the _Pair_ concept (wherever that is defined).

---
