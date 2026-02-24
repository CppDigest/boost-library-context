# #54 - Requesting zip_tail / zip_tail_with [Closed]

> Username: brevzin  
> Created at: 2020-08-10 23:04:21 UTC  
> Updated at: 2020-10-14 21:38:17 UTC  
> Closed at: 2020-10-14 21:38:16 UTC  
> Url: https://github.com/boostorg/mp11/issues/54  

I'm not sure what the right name for these should be (the Python itertools module has an example for the first one that is named `pairwise`). These are fairly trivial to implement:  
  
```cpp  
template <typename L, template <typename...> class F>  
using mp_zip_tail_with = mp_transform<F, mp_pop_back<L>, mp_pop_front<L>>;  
  
template <typename L>  
using mp_zip_tail = mp_zip_tail_with<L, mp_list>;  
```  
  
But seem useful enough that should come batteries included (and possibly there's a more efficient implementation that doesn't rely on `mp_pop_back`).  
  
On that note, `mp_transform` currently requires all the lists to have the same length. I think that's worth adding to the documentation (not really obvious, and the error if you get it wrong isn't particularly informative). Is it worth having a version of this that just takes the shortest list as `zip`?

---

## Comment 1

> Username: pdimov  
> Created at: 2020-08-10 23:18:14 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-671636507  

zip_tail? Where does this name come from?

---

## Comment 2

> Username: pdimov  
> Created at: 2020-08-10 23:18:55 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-671636703  

And what is the use case?

---

## Comment 3

> Username: brevzin  
> Created at: 2020-08-10 23:48:33 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-671645021  

> zip_tail? Where does this name come from?  
  
In Haskell it's like `\x -> zip x (tail x)` or `ap zip tail`. You're `zip`-ing a list with its own `tail`.  
  
> And what is the use case?  
  
Any time you need to do some operation on consecutive pairs of elements in a single list. Checking if a list is sorted, for instance, is `mp_rename<mp_zip_tail_with<L, mp_less>, mp_all>`. C++ calls this `adjacent_difference` (which is such a bad name that people use binary `std::transform` anyway).

---

## Comment 4

> Username: pdimov  
> Created at: 2020-08-11 17:43:03 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-672127459  

I like the horrible name, although not the `difference` part. `mp_adjacent_transform` perhaps. `mp_adjacent` is shorter but a bit cryptic. `mp_pairwise_transform` also works, but `adjacent` is more precise.  
  
I can't think of a way to avoid the `mp_pop_back` here.

---

## Comment 5

> Username: brevzin  
> Created at: 2020-08-11 18:25:46 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-672165854  

> I like the horrible name, although not the `difference` part.  
  
That's the horrible part! 😂

---

## Comment 6

> Username: pdimov  
> Created at: 2020-08-11 19:08:42 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-672203465  

Since the result has one fewer element, applying this transform to a list with one element yields an empty list. But, as written, applying it to a list with zero elements is a substitution failure, which breaks the simple is_sorted above. Yielding an empty list in this case as well will arguably be more usable, but less consistent. Thoughts?

---

## Comment 7

> Username: brevzin  
> Created at: 2020-08-11 19:30:30 UTC  
> Updated at: 2020-08-11 19:31:30 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-672219179  

Yeah, I think yielding an empty list is the right thing. It's good enough for Haskell:  
  
```haskell  
> zip_tail = \x -> zip x (tail x)  
> zip_tail []  
=> []  
> zip_tail [1]  
=> []  
> zip_tail [1, 2]  
=> [(1,2)]  
```  
  
Even though `tail []` throws:  
  
```haskell  
> tail []  
*** Exception: Prelude.tail: empty list  
```

---

## Comment 8

> Username: pdimov  
> Created at: 2020-10-12 16:21:38 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-707218299  

Eh, mp_transform<F, L> but mp_adjacent_transform<L, F>... Why are trivial things always so hard.

---

## Comment 9

> Username: brevzin  
> Created at: 2020-10-12 16:24:16 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-707219631  

> Eh, mp_transform<F, L> but mp_adjacent_transform<L, F>... Why are trivial things always so hard.  
  
Yeah for proposing adding new views to C++23, I think the names we're going for are adjacent and adjacent_transform rather than zip_tail and zip_tail_with. That'll be in the October mailing.

---

## Comment 10

> Username: pdimov  
> Created at: 2020-10-12 17:47:14 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-707260179  

My problem is with the inconsistent argument order though.

---

## Comment 11

> Username: pdimov  
> Created at: 2020-10-14 21:38:16 UTC  
> Url: https://github.com/boostorg/mp11/issues/54#issuecomment-708674489  

`mp_pairwise_fold` it is.
