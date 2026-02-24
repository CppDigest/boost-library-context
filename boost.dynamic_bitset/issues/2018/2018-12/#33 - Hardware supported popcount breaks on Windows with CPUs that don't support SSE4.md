# #33 - Hardware supported popcount breaks on Windows with CPUs that don't support SSE4 [Closed]

> Username: montekarlos  
> Created at: 2018-12-18 11:25:13 UTC  
> Updated at: 2019-02-22 13:36:13 UTC  
> Closed at: 2019-02-22 13:36:13 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33  

When using boost v1.69 dynamic_bitset::count() will crash on CPUs without SSE4 (required for popcount) when compiled using MSVC v15.9.4 and run on Windows 7. The was compiled as Release x64  
  
Attached is example project to recreate: [PopcountCrash.zip](https://github.com/boostorg/dynamic_bitset/files/2690228/PopcountCrash.zip)  
  
I've had a quick look at the [PR](https://github.com/boostorg/dynamic_bitset/pull/26), [issue](https://github.com/boostorg/dynamic_bitset/issues/25) and linked mailing list [discussion](https://lists.boost.org/Archives/boost/2018/08/242825.php)  
  
It appears there was some concern about handling hardware support for MSVC but I can't see anything in the PR or commit that would handle CPUs without SSE4 (i.e [__cpuid check](https://lists.boost.org/Archives/boost/2018/08/242840.php))

---

## Comment 1

> Username: jeking3  
> Created at: 2018-12-18 12:28:53 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-448204163  

cc: @Izaron

---

## Comment 2

> Username: jeking3  
> Created at: 2019-01-05 00:03:53 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-451605360  

cc: @Izaron

---

## Comment 3

> Username: glenfe  
> Created at: 2019-02-20 02:49:03 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-465398888  

What is the plan if the user being CC'd doesn't respond by 1.70 deadlines? Is the change that was committed going to be reverted for 1.70?

---

## Comment 4

> Username: jeking3  
> Created at: 2019-02-20 03:11:46 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-465403642  

That's the easiest thing to do, unless someone else steps up to resolve it.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-02-21 01:36:31 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-465825999  

James, since you committed the PR, it's your responsibility to deal with the regression/crash, one way or another. (The gcc __builtin_popcount path is fine, but the msvc-specific section isn't, so there are options, but something must be done.)

---

## Comment 6

> Username: jeking3  
> Created at: 2019-02-21 13:01:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-465991492  

Okay, thanks.

---

## Comment 7

> Username: jeking3  
> Created at: 2019-02-21 15:11:27 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-466035781  

@pdimov what would your preference be to resolve this:  
  
1. Disable the MSVC code (easiest).  
1. Add a definition such as `BOOST_DYNAMIC_BITSET_HAVE_SSE4` that folks must define in order to enable the code (also easy, requires documentation).  Of course, this means they know their code will always run on a modern CPU so it's their choice.  
1. Add static cpuid inspection (ideally C++11 only, so it is threadsafe) and fall back when not present (more difficult, may not have time to do...).  
  
Right now I would opt for one of the first two, which makes the library no worse than it was in 1.68.0 for MSVC.  It's unfortunate the original author @Izaron hasn't responded or taken action.  For now we could do (1) and then open an issue for (3).

---

## Comment 8

> Username: glenfe  
> Created at: 2019-02-21 15:47:23 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-466049968  

I suggest #2 and document it accordingly: i.e. The opt-in macro would only affect MSVC; for GCC and Clang using __builtin_popcount unconditionally is fine. (#1 is also acceptable if you don't want to do #2).

---

## Comment 9

> Username: pdimov  
> Created at: 2019-02-21 16:00:22 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-466055197  

(1) is fine if we don't want to invest too much time into it.  
  
On the surface silently doing the right thing, as in (3), is better than (2), but I'm not sure if the two tests - on the static guard variable and on the actual static variable - would not defeat the gains from using the popcnt instruction.  
  
There is also the option of using an alternate popcount algorithm - the one used by gcc/clang for implementing the builtin when SSE4 isn't available, https://godbolt.org/z/VGP6Z_, https://en.wikipedia.org/wiki/Hamming_weight. This will need to be benchmarked if we choose to use it (on MSVC or in general).

---

## Comment 10

> Username: jeking3  
> Created at: 2019-02-21 16:36:17 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-466069552  

I was looking at https://github.com/kimwalisch/libpopcnt/ as there is an implementation in there as well, but I haven't done anything else but look.  Benchmarks exist there for the decisions that were made to use different algorithms depending on the size of the bitset.

---

## Comment 11

> Username: pdimov  
> Created at: 2019-02-21 16:50:03 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/33#issuecomment-466075306  

FWIW, `std::bitset` on MSVC uses the same 8 bit table-based implementation as `dynamic_bitset`: https://godbolt.org/z/YuQqIj  
  
This probably means that it was judged optimal there in the absence of hardware popcnt.
