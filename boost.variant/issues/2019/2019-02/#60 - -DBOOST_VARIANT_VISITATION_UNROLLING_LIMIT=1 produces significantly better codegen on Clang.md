# #60 - -DBOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1 produces significantly better codegen on Clang [Closed]

> Username: pdimov  
> Created at: 2019-02-27 23:27:29 UTC  
> Updated at: 2020-01-23 07:28:13 UTC  
> Closed at: 2020-01-23 07:28:12 UTC  
> Url: https://github.com/boostorg/variant/issues/60  

The current visitation implementation always generates a switch with 20 cases, regardless of the number of alternatives in the variant. For the common case of 2-6 alternatives, this is a significant pessimization under Clang, because it doesn't inline the visitation: f.ex. https://godbolt.org/z/TxiL_G  
  
With -DBOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1, Clang is smart enough to turn the recursive calls back into a switch, with the correct number of cases: https://godbolt.org/z/3Zxu4X  
  
This results in a measurable difference, which I discovered because I was asked to produce benchmarks of Variant2, see f.ex.  
  
https://github.com/pdimov/variant2/blob/develop/benchmark/benchmark1.md  
https://github.com/pdimov/variant2/blob/develop/benchmark/benchmark2.md  
  
where Boost.Variant suffers because of this issue, but is significantly faster than the competition with -DBOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1 under both g++ and clang++.  
  
Perhaps the decision to use the preprocessor-based unrolled switch needs to be revisited for the more modern compilers.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-03-03 22:56:02 UTC  
> Updated at: 2019-03-03 22:56:12 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-469074134  

Strange. I saw not a long ago somewhere (most likely on GCC bugtracker) that `boost::variant` is faster than `std::variant` because of using switch and those folks suggested to make something like this to libstdc++ implementation.

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-03-23 10:25:23 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-475858246  

Looks like GCC also generates a better code with `-DBOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1`.  
  
Good news everyone! It means that I can drop the macro stuff and just leave the recursive calls. This should simplify the code, improve the build times and make the library more modules friendly :)

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-03-23 23:08:04 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-475912497  

Interesting, it contradicts https://github.com/mpark/variant/pull/56.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-03-23 23:36:16 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-475914059  

You can always try the benchmark and see, I suppose.

---

## Comment 5

> Username: apolukhin  
> Created at: 2019-03-24 04:54:35 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-475928375  

> Interesting, it contradicts [mpark/variant#56](https://github.com/mpark/variant/pull/56).  
  
It looks that the initial version was using type erased array of functions (just like GCCs std::variant does). Compilers are bad in inlining and optimizing functions by function pointer https://gcc.gnu.org/bugzilla/show_bug.cgi?id=86912  
  
I assume that `switch` is faster than an array of functions, while the trivial tail recursion is faster than `switch`.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-03-24 12:58:55 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-475956482  

The problem with the current implementation is not that it's a switch, but that it's a switch with 20 cases. When the variant has only three alternatives, the optimal switch has three (or four) cases, not 20. (In fact, the compilers turn the tail recursion back into a switch.)

---

## Comment 7

> Username: apolukhin  
> Created at: 2019-03-25 20:58:11 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-476374728  

I've done a small research. Note that the DIFF View is often messed up when using the links (so you have to manually adjust it):  
* With small amount of types in variant https://godbolt.org/z/YtUYIb and -DBOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1 assembly is:  
    * much better on Clang (it removes the crazy jump tables... Those were already noted in https://bugs.llvm.org/show_bug.cgi?id=39254 )  
    * exactly the same on GCC  
    * much worse on MSVC  
* With a lot of of types in variant https://godbolt.org/z/6GIZUU and -DBOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1 assembly is:  
    * slightly worse on Clang  
    * much better on GCC  
    * better on MSVC  
  
I was weighting the assembly quality mostly be the amount of assembly data in the result.  
  
The whole story has a bad small. In ideal world compilers should be able to optimize the switch with any value of `BOOST_VARIANT_VISITATION_UNROLLING_LIMIT` to the same assembly. With values other than 1 it could be a little bit harder and will add some compile time overhead for small amount of types.  
  
Right now I'm in favor of leaving the `BOOST_VARIANT_VISITATION_UNROLLING_LIMIT` as is.

---

## Comment 8

> Username: Kojoley  
> Created at: 2019-03-25 21:21:37 UTC  
> Updated at: 2019-03-26 01:54:17 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-476382413  

It seems that GCC and MSVC are merging the same branches while Clang not. Placing `__builtin_unreachable` inside `forced_return` heals the things for Clang. https://godbolt.org/z/_njW1p  
  
Seems to be related bug report https://bugs.llvm.org/show_bug.cgi?id=24220.

---

## Comment 9

> Username: apolukhin  
> Created at: 2020-01-22 20:48:10 UTC  
> Updated at: 2020-01-22 20:48:55 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-577378289  

Seems that `forced_return` changed the picture and now all the compilers produce quite the same assembly in most cases: https://godbolt.org/z/dJqYjc and https://godbolt.org/z/UDGLJF  
  
The major difference is that with `BOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1` and small amount of types MSVC produces shorter codegen.

---

## Comment 10

> Username: apolukhin  
> Created at: 2020-01-22 21:25:18 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-577393013  

`BOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1` speeds up compilation:  
time ../../../b2 toolset=clang-9,gcc-8 cxxstd=14,11,03,98 -j4 -a  
with defaults, second run:  
```  
real    6m25.725s  
user    22m41.421s  
sys    1m17.262s  
```  
vs `BOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1` run  
```  
real    6m18.966s  
user    22m9.030s  
sys    1m14.541s

---

## Comment 11

> Username: Kojoley  
> Created at: 2020-01-22 21:39:53 UTC  
> Updated at: 2020-01-22 21:42:07 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-577398623  

There are two key differences between them:  
  1. Clang handles branches and switches the same way, it is able to combine branches into switches https://godbolt.org/z/6tkZNw and to merge switches https://godbolt.org/z/P7wGPB together, while GCC and MSVC are not able to do anything of that. The GCC bug reports are https://gcc.gnu.org/bugzilla/show_bug.cgi?id=14799 and https://bugs.llvm.org/show_bug.cgi?id=24220; you seem to be following them :-)  
  2. GCC is able to merge same switch cases while others are not. Because of that GCC does not even hit a jumptable generation threshold and the resulting asm is same for both cases (`BOOST_VARIANT_VISITATION_UNROLLING_LIMIT=1` and default).  
  
385s vs 378s is about 2% difference, it could be just dynamic clocking fluctuation if you did not turn it off or/and took measurement multiple times.

---

## Comment 12

> Username: apolukhin  
> Created at: 2020-01-22 22:18:04 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-577412426  

I've measured multiple times, the difference in time holds. But it is minor, you're right.

---

## Comment 13

> Username: apolukhin  
> Created at: 2020-01-23 07:28:12 UTC  
> Url: https://github.com/boostorg/variant/issues/60#issuecomment-577539892  

I'm closing this issue: there's no solution that improves all the compilers codegens in all the workloads.
