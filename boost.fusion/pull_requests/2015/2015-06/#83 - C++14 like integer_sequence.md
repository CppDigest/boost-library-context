# #83 C++14 like integer_sequence. [Merged]

> Username: Flast  
> Created at: 2015-06-21 08:56:49 UTC  
> Updated at: 2015-06-22 14:18:41 UTC  
> Merged at: 2015-06-22 13:38:44 UTC  
> Closed at: 2015-06-22 13:38:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/83  

This PR introduces the helper for implementing variadic containers.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-06-21 12:39:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113894687  

Is there a boost.config switch that detects the availability of C++14 integer_sequence so we can use it if available? I'm not sure however if that gives us any advantage. Thoughts?

---

## Comment 2

> Username: K-ballo  
> Created_at: 2015-06-21 14:14:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113907145  

This implementation looks linear, an efficient implementation would be at least logarithmic. The use of typed index creation means memoization is lost across sequences of different types, although I doubt this will ever be used with something other than `std::size_t`. The use of `mpl` is likely to be unnecessarily cutting useful recursion depth.  
  
> Is there a boost.config switch that detects the availability of C++14 integer_sequence so we can use it if available? I'm not sure however if that gives us any advantage. Thoughts?  
  
IIRC, _libc++_ implementation is log8, and while _libstdc++_ seriously considered deferring to a builtin for efficiency reasons they ended up with a linear one, so it can go either way. It's probably safer to just stick to a single implementation and make that one efficient, since it's the cornerstone of efficient variadics.

---

## Comment 3

> Username: Flast  
> Created_at: 2015-06-21 15:35:14 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113915515  

> Is there a boost.config switch that detects the availability of C++14 integer_sequence so we can use it if available? I'm not sure however if that gives us any advantage. Thoughts?  
  
Yes, but currently boost.config doesn't provide any such macros.  
  
> This implementation looks linear, an efficient implementation would be at least logarithmic.  
  
Yes, this implementation is linear and I know that we can implement at least logarithmic.  
  
However, I think the obvious implementation is better in first implementation to tell a minimal requirement for implementing variadic containers.

---

## Comment 4

> Username: K-ballo  
> Created_at: 2015-06-21 18:52:14 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113941298  

> This implementation looks linear, an efficient implementation would be at least logarithmic.  
>   
> Yes, this implementation is linear and I know that we can implement at least logarithmic.  
>   
> However, I think the obvious implementation is better in first implementation to tell a minimal requirement for implementing variadic containers.  
  
I would like to profile your variadic sequences implementation in the upcoming PRs. Index sequence generation is the only place in which recursive instantiations are needed. This will be a dominant factor due to the superlinear name mangling effect. Effective use of memoization is much more subtle, specially in cases where there is a single container involved (and thus a single index sequence).  
  
If compile time performance is not a concern, please disregard.

---

## Comment 5

> Username: djowel  
> Created_at: 2015-06-21 22:35:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113965183  

Given all these, I think it makes sense to make the implementation 'switchable' to the std if C++14 is available. That way, we can profile and test various compilers and use which one is faster. Of course compile time is a concern.

---

## Comment 6

> Username: K-ballo  
> Created_at: 2015-06-21 22:47:22 UTC  
> Updated_at: 2015-06-21 22:48:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113965588  

> Given all these, I think it makes sense to make the implementation 'switchable' to the std if C++14 is available. That way, we can profile and test various compilers and use which one is faster.   
  
I suggest against this. We can implement this in a way that's on par (or even outperforms) the best implementation available out there today. That would give us portable performance.  
  
> Of course compile time is a concern.  
  
Of course compile time is a concern, that's out of the question. What I meant was that if efficient compile times is a goal of the variadic sequences PRs to come, I can provide my insights on the matter.  
  
Here is a sample: https://gist.github.com/K-ballo/2f7a812691d4fad7b84e  
  
The worst implementation in there, the linear one, takes 1.10seconds to compile. The one in this PR takes roughly 3.20seconds, and most of the gap can be attributed to `mpl::if_`. Replacing it with `std::conditional` (to cut the cruft, I'm not suggesting this) and dropping the redundant `mpl::identity` brings it down to 2.50seconds (keeping the `#includes` in).  
  
The best implementation in there takes 0.04seconds, and uses ~6Mb of memory instead of the ~95Mb or so here. For refence, libc++'s implementation takes about 0.07seconds and uses ~8Mb.

---

## Comment 7

> Username: djowel  
> Created_at: 2015-06-21 22:53:55 UTC  
> Updated_at: 2015-06-21 22:56:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113966017  

OK, that makes sense, K-ballo. I defer to your expertise in this matter. We should use your implementation then. Compile time is a BIG concern and your insights will be invaluable!

---

## Comment 8

> Username: K-ballo  
> Created_at: 2015-06-21 23:18:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113968013  

@Flast: You are the one doing all the hard work, it's entirely up to you. Just let me know.

---

## Comment 9

> Username: Flast  
> Created_at: 2015-06-21 23:43:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113969274  

> Here is a sample: https://gist.github.com/K-ballo/2f7a812691d4fad7b84e  
>   
> The worst implementation in there, the linear one, takes 1.10seconds to compile. The one in this PR takes roughly 3.20seconds, and most of the gap can be attributed to mpl::if_. Replacing it with std::conditional (to cut the cruft, I'm not suggesting this) and dropping the redundant mpl::identity brings it down to 2.50seconds (keeping the #includes in).  
>   
> The best implementation in there takes 0.04seconds, and uses ~6Mb of memory instead of the ~95Mb or so here. For refence, libc++'s implementation takes about 0.07seconds and uses ~8Mb.  
  
Wow! Good survey.  
  
OK, I'll import @K-ballo 's implementation and test with some compilers.

---

## Comment 10

> Username: djowel  
> Created_at: 2015-06-21 23:52:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113969633  

Indeed! Thanks for sharing, @K-ballo! @Flast I think we should make all future code profiler driven, like what @K-ballo did here. Let's try to make it as optimized as we can. I know it will require more work, but I think it will all be worth it.  
  
Many thanks, @K-ballo and @Flast !!!

---

## Comment 11

> Username: K-ballo  
> Created_at: 2015-06-22 00:07:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113971060  

> I think we should make all future code profiler driven, like what @K-ballo did here. Let's try to make it as optimized as we can. I know it will require more work, but I think it will all be worth it.  
  
I'd be happy to help with that. I've been doing it for a long time, and I'm familiar with the patterns that increase compilation time as well as those that trigger compiler bugs.

---

## Comment 12

> Username: Flast  
> Created_at: 2015-06-22 00:44:35 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113973762  

> I doubt this will ever be used with something other than std::size_t.  
  
Why I don't implement `index_sequence` directly, see [screenshot of my tweet](https://twitter.com/Flast_RO/status/602369534352195584) (MSVC's strange behaviour). It will affect [`set` implementation](https://github.com/Flast/fusion/commit/a3d058642cf5c28108b0c639dfb90df40c1f394c).

---

## Comment 13

> Username: K-ballo  
> Created_at: 2015-06-22 01:28:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113978691  

> I doubt this will ever be used with something other than std::size_t.  
>   
> Why I don't implement index_sequence directly, see screenshot of my tweet (MSVC's strange behaviour). It will affect set implementation.  
  
You should only ever need an `index_sequence` for internal use. If you need otherwise, you can start from an `index_sequence` and convert it as appropriate. I haven't looked in detail at the set implementation, but it does not look like a sequence of `int`s is needed, you'd possibly be fine by just round-tripping the conversion via a helper static const.

---

## Comment 14

> Username: Flast  
> Created_at: 2015-06-22 02:24:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113982955  

> You should only ever need an index_sequence for internal use.  
  
Yes.  
  
> If you need otherwise, you can start from an index_sequence and convert it as appropriate. I haven't looked in detail at the set implementation, but it does not look like a sequence of ints is needed, you'd possibly be fine by just round-tripping the conversion via a helper static const.  
  
OK. Now I use `index_sequence` instead of `integer_sequence`.  
  
@K-ballo , BTW, can I write your name into copyright notice?

---

## Comment 15

> Username: K-ballo  
> Created_at: 2015-06-22 03:01:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113988804  

> @K-ballo , BTW, can I write your name into copyright notice?  
  
Sure, are you going with something based in one of my implementations? Note that the last two are incomplete, they should recurse on numbers >= 256 (or static_assert or whatever as long as higher bits are not ignored). The point of the last one is merely to show that the winnings of the previous one are not due to the reduced number of instantiations (log8), whose effect is pretty much insignificant, but on exploiting memoization. Even while they halve the time taken by the simple log2 one, I might see that as insignificant too; the crucial point is diminishing the superlineal mangling effect.

---

## Comment 16

> Username: Flast  
> Created_at: 2015-06-22 03:31:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113992711  

> Sure, are you going with something based in one of my implementations? Note that the last two are incomplete, they should recurse on numbers >= 256 (or static_assert or whatever as long as higher bits are not ignored).   
  
OK, I use approach 1 (log2) tentatively.

---

## Comment 17

> Username: Flast  
> Created_at: 2015-06-22 04:31:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-113997735  

Now it is replaced with @K-ballo 's log2 impl and added test for `index_sequence`.

---

## Comment 18

> Username: K-ballo  
> Created_at: 2015-06-22 13:05:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-114095271  

LGTM

---

## Comment 19

> Username: Flast  
> Created_at: 2015-06-22 14:05:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-114114521  

Thank you both!

---

## Comment 20

> Username: djowel  
> Created_at: 2015-06-22 14:18:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/83#issuecomment-114125446  

Thank you! You did all the hard work! And @K-ballo, you rock!

---
