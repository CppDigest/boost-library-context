# #273 - Consider adding index retrieval functions [Open]

> Username: vittorioromeo  
> Created at: 2016-05-19 14:22:36 UTC  
> Updated at: 2017-07-07 06:05:03 UTC  
> Url: https://github.com/boostorg/hana/issues/273  

I encountered the need of functions that can give me the indices of elements matching a specific predicate in a sequence in a real development situation. I propose the addition of the following functions, whose goal is to allow users to quickly retrieve the indices of elements in sequences depending on a predicate:  
- `hana::indices_of_matching`  
- `hana::indices_of`  
- `hana::index_of_first_matching`  
- `hana::index_of`  
  
---  
  
`hana::indices_of_matching(xs, predicate)` returns a `Sequence` containing all the indices of the elements in `xs` that match `predicate`.  
  
```  
// pseudocode  
hana::indices_of_matching = [](auto&& xs, auto&& predicate) {  
        return hana::make_basic_tuple(/* some hana::size_c indices ...*/);  
    }  
```  
  
---  
  
`hana::indices_of(xs, key)` returns a `Sequence` containing all the indices of the elements in `xs` that are equal to `key`.  
  
```  
// pseudocode  
hana::indices_of = [](auto&& xs, auto&& key) {  
        return hana::make_basic_tuple(/* some hana::size_c indices ...*/);  
    }  
```  
  
Satisfies:  
  
`indices_of(xs, key) == indices_of_matching(xs, equal.to(key))`  
  
---  
  
`hana::index_of_first_matching(xs, predicate)` returns an `hana::optional<hana::size_t>` contaning:  
- `hana::just<x>`: if `xs` contains at least one element matching `predicate`, where `x` is the index of the first such element.  
- `hana::none`: if `xs` does not contain any element matching `predicate`.  
  
---  
  
`hana::index_of(xs, key)` returns an `hana::optional<hana::size_t>` contaning:  
- `hana::just<x>`: if `xs` contains at least one element equal to `key`, where `x` is the index of the first such element.  
- `hana::none`: if `xs` does not contain any element equal to `key`.  
  
Satisfies:  
  
`index_of(xs, key) == index_of_first_matching(xs, equal.to(key))`  
  
---  
  
---  
  
In addition, I propose some functions to "select" a subsequence of elements given a sequence of indices, and functions to invert the selection:  
- `hana::invert_indices`  
- `hana::slice_inverse`  
  
---  
  
`hana::invert_indices(xs, indices)`: given a `Sequence` `xs` and a `Sequence` of indices `indices`, return the full sequence of `xs`'s indices minus `indices`. (It "filters out" indices).  
  
Example:  
  
```  
// pseudocode  
auto seq = [a, b, c, d, e, f]; // length = 6  
auto indices = [0, 3, 4];  
auto inv_indices = hana::invert_indices(seq, indices);  
assert(inv_indices == [1, 2, 5]);  
```  
  
Satisfies:  
  
`length(hana::invert_indices(xs, indices)) == length(xs) - length(indices)`.  
  
---  
  
`hana::slice_inverse(xs, indices)`: given a `Sequence` `xs` and a `Sequence` of indices `indices`, returns a subset of `xs` only containing the elements at positions not in `indices`.  
  
Satisfies:  
  
`hana::slice_inverse(xs, idxs) == hana::slice(xs, hana::invert_indices(xs, idxs))`.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-05-19 16:07:44 UTC  
> Updated at: 2016-05-19 16:09:43 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-220372937  

I kind of like the name `index_if` for the function that takes a predicate, of that is not too easily confused with `index_of`. It would be consistent with existing functions like `find_if`.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-05-20 02:53:13 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-220505154  

This has been requested before on [StackOverflow](http://stackoverflow.com/q/33979592/627587). I'll add this to the library in a future version, since there seems to be interest.

---

## Comment 3

> Username: ldionne  
> Created at: 2017-03-29 15:27:36 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-290126534  

I just needed that again for another [StackOverflow](http://stackoverflow.com/q/43089587/627587) question. There really is a need for this.

---

## Comment 4

> Username: ricejasonf  
> Created at: 2017-03-29 17:54:06 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-290170244  

For that last one, I suggested using a map.

---

## Comment 5

> Username: ScottFreeCode  
> Created at: 2017-03-30 00:29:25 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-290266444  

> I kind of like the name `index_if` for the function that takes a predicate, of that is not too easily confused with `index_of`.  
  
I see what you did there. 😆

---

## Comment 6

> Username: ricejasonf  
> Created at: 2017-04-05 04:50:02 UTC  
> Updated at: 2017-04-05 04:53:13 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-291753411  

I can do a PR for `index_if` if you're interested. I'd almost think that it would have to be for `Sequence` as `Iterable` is not necessarily finite.  
  
It could behave like `detail::index_if` in that it returns an out of bounds index if no element satisfied the predicate.  
  
fwiw I'm currently using `detail::index_if` to get around not being able to get an element by its type  
```cpp  
  template<typename Tag>  
  struct get_impl<Tag, hana::when<hana::Searchable<Tag>::value>>  
  {  
    template <typename Store, typename Key>  
    static constexpr decltype(auto) apply(Store&& s, Key&& k)  
    {  
      if constexpr(hana::Sequence<Store>::value)  
      {  
        // FIXME using hana::detail  
        using Pred = decltype(hana::compose(hana::equal.to(hana::typeid_(k)), hana::typeid_));  
        using Pack = typename hana::detail::make_pack<Store>::type;  
        return hana::at_c<hana::detail::index_if<Pred, Pack>::value>(  
          std::forward<Store>(s)  
        );  
      }  
      else  
      {  
        return hana::at_key(std::forward<Store>(s), std::forward<Key>(k));  
      }  
    }  
  };  
```

---

## Comment 7

> Username: ldionne  
> Created at: 2017-04-05 04:54:28 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-291753969  

Would love that. Hmm, could it be defined on `Iterable`s that are also `Foldable`? Basically, `Foldable` means they have to be finite, so I think that would do the trick.  
  
Actually, if I think about it, we could also implement if on infinite `Iterable`s, but it would never terminate if the predicate does not return true at a finite index.

---

## Comment 8

> Username: ldionne  
> Created at: 2017-04-07 23:21:00 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-292674164  

This has been partially addressed by #329.

---

## Comment 9

> Username: ricejasonf  
> Created at: 2017-07-05 22:53:33 UTC  
> Updated at: 2017-07-05 22:56:41 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-313248618  

The `indices_of_matching` function was also requested here https://stackoverflow.com/questions/44935075/sequence-of-indices-of-tuple-elements-satifying-predicate-in-hana  
  
What if we called it `filter_indices`? I could see a `filtered` view using this as well.  
  
If it is welcome I can do a PR for this. Let me know.  
  
I just noticed there is a `detail::filter_indices` too.

---

## Comment 10

> Username: ldionne  
> Created at: 2017-07-07 05:39:02 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-313591674  

I'm somewhat uneasy about adding this, since it seems a bit special-purpose. For example, there is nothing like this in the C++ standard library (for iterators), since this can be built easily enough on top of existing algorithms. Also, the SO answer shows:  
  
```c++  
constexpr auto get_indices_of = [](auto tuple, auto predicate){  
    constexpr auto indices = to<tuple_tag>(range_c<std::size_t, 0, size(tuple)>);  
    return filter(indices, [=](auto i){ return predicate(tuple[i]); });   
};  
```  
  
which seems like a perfectly valid (and simple enough) way of implementing this using Hana. I want to be careful to keep the interface somewhat minimal to avoid bloat and loss of consistency.  
  
Out of curiosity, what concept would an hypothetical `filter_indices` be associated to?

---

## Comment 11

> Username: ricejasonf  
> Created at: 2017-07-07 06:03:53 UTC  
> Updated at: 2017-07-07 06:05:03 UTC  
> Url: https://github.com/boostorg/hana/issues/273#issuecomment-313594921  

Since it would be just folding an index sequence from an `Iterable`, I would say it would be for `Iterable` just like `index_if`.  
  
I remember you mentioning something about a `Sliceable` concept a while back that would assume some of the algorithms currently under `MonadPlus`.  
  
BTW I'm not hardcore for this or anything.
