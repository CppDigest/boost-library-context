# #268 - Consider optimizing folds by not using variadic expansion [Open]

> Username: ldionne  
> Created at: 2016-04-20 01:16:46 UTC  
> Updated at: 2016-04-20 14:16:53 UTC  
> Url: https://github.com/boostorg/hana/issues/268  

Right now, the folds are implemented by unpacking the sequences and then folding recursively on variadic packs (with some unrolling). However, passing many parameters around can be difficult to optimize away, and overload resolution is expensive in terms of compile-time. Instead, we should consider having a unrolled recursive fold that indexes into the original structure.

---

## Comment 1

> Username: jfalcou  
> Created at: 2016-04-20 05:15:16 UTC  
> Updated at: 2016-04-20 05:15:39 UTC  
> Url: https://github.com/boostorg/hana/issues/268#issuecomment-212261144  

We use ... and a creative use of the for_each_args like function in Boost.SIMD to perform runtime fold. Not sure it helps you but it still doesnt require recursion :   
  
https://github.com/NumScale/boost.simd/blob/develop/include/boost/simd/arch/common/generic/function/autofold.hpp#L47  
  
(extract<N>(a) is basically a[N] fyi)  
  
Slap me if I misunderstood what you wanted here. It's skewed toward numeric reduction (defined by a binary op and a neutral element) but who knows.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-04-20 14:02:08 UTC  
> Url: https://github.com/boostorg/hana/issues/268#issuecomment-212438529  

Interesting, but IIUC the result type of your fold is completely determined by the result of calling the function `F` on your `Arg::value_type`, which is fixed. In other words, that's not a heterogeneous fold, it's an homogeneous fold, albeit an unrolled one.

---

## Comment 3

> Username: jfalcou  
> Created at: 2016-04-20 14:04:06 UTC  
> Url: https://github.com/boostorg/hana/issues/268#issuecomment-212439384  

Yes, as I said, i was more showing the trick with the unrolling. I _think_ the initialisation can be made heterogeneous though.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-04-20 14:10:40 UTC  
> Url: https://github.com/boostorg/hana/issues/268#issuecomment-212441192  

Oh, ah okay I see. Yeah there's a way to do it but it requires placement new, which can't be constexpr. Otherwise you have to require that every intermediate type is default constructible and assignable, which is too strict for us.

---

## Comment 5

> Username: ldionne  
> Created at: 2016-04-20 14:11:37 UTC  
> Url: https://github.com/boostorg/hana/issues/268#issuecomment-212441528  

The idea of what I'm talking about is implemented [here](https://github.com/boostorg/hana/blob/heterogeneous_storage/experimental/heterogeneous_storage_scan_left.hpp#L44). It's a scan, not a fold, but the fold is easy to derive from the scan implementation.

---

## Comment 6

> Username: pfultz2  
> Created at: 2016-04-20 14:15:06 UTC  
> Url: https://github.com/boostorg/hana/issues/268#issuecomment-212443103  

You couldn't do something like:  
  
``` cpp  
template<class Index, class Sequence, class State, class Op>  
constexpr auto fold_3(Index i, Sequence s, State state, Op op)  
{  
    auto state1 = op(state, s[i]);  
    auto state2 = op(state1, s[i+1]);  
    return op(state2, s[i+2]);  
}  
```

---

## Comment 7

> Username: ldionne  
> Created at: 2016-04-20 14:16:20 UTC  
> Url: https://github.com/boostorg/hana/issues/268#issuecomment-212443790  

Yeah, that's exactly what I had in mind.

---

## Comment 8

> Username: ldionne  
> Created at: 2016-04-20 14:16:53 UTC  
> Url: https://github.com/boostorg/hana/issues/268#issuecomment-212443987  

We'd have a few of them unrolled, and then we'd chain unrolled calls until the whole length of the sequence has been processed.
