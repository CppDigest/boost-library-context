# #315 Simplify and provide kara mul for cpp_dec_float [Merged]

> Username: ckormanyos  
> Created at: 2021-04-04 11:43:44 UTC  
> Updated at: 2021-04-09 15:53:12 UTC  
> Merged at: 2021-04-08 14:05:14 UTC  
> Closed at: 2021-04-08 14:05:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315  

This draft pull request is the first of potentially several attempts to simplify and improve `cpp_dec_float`.  
  
In this draft PR, we will investigate various simplifications, attempts to make `cpp_dec_float` more _exact_ and add Karatsuba multiplication for large digit counts.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-04-04 11:48:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-813019594  

The purpose of 67f30ae9a6739235e61d3390da01087529ad1f2a is to increase synergy between the two types of containers (static/dynamic) used in `cpp_dec_float`.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-04-05 08:02:24 UTC  
> Updated_at: 2021-04-05 08:05:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-813260205  

This recent big push cleans up `constexpr` in the sense that some new compilers correctly note that `initializer_list` seems to not be a true compile-time aggregate (until C++20) and can not be used in `constexpr` constructors.  
  
After that trivial change, certain  code sequences have been cleaned up.  
  
The big try in this push, however, is to use the mathematics of Karatsuba multiplication with the base-10 represenation of `cpp_dec_float`. Empirical observations show that the numbers work out fine. The performance improvement, however, is (seeminlgy non-intuitively) not present for low digit counts. in fact, the n*n->2n multiplication scheme is selectively used in this first draft and this actually has the consequencs of slightly decreasing performance for digit counts below about 1,000.  
  
For high digit counts, however, the performance of Karatsuba kicks in. Also the limitation on 1,800 limbs is removed, allowing for higher open-ended ranges up to many, many thousands of digits.  
  
A second iteration (if/when CI runs) could be to restore the _half triangle_ multiplication which was previously used. In this sense, make sure that the Karatsuba multiplication is only dispatched and supported via compile-time enable based on the number of limbs (i.e., there is no need to clutter up low-digit compilations with Karatsuba stuff if it will not even be used).  
  
So let's see how (or if) CI runs and pans out...

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-04-06 09:05:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-813959893  

There is, as of late, a bit of a traffic jam on GHA. I might take a break for a while or check in with ci skip and look at drone results.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-04-06 11:48:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-814056460  

So Karatsuba multiplication is in and looking good on drone.  
  
In a round-about fashion, I first took away the n-by-n-to-1n multiplication scheme, which had been used for years and replaced it with (even for low digit counts) an n-by-n-to-2n multiplication scheme. This is used as the base-case for Karatsuba multiplication.  
  
The next steps will involve a few refinements:  
  
- Select the multiplication scheme that is dispatched via template compile-time enable-if.  
- In this way, only include the overhead of Karatsuba multiplication in code and RAM if it is actually intended to be used.  
- When doing this, the question arises if one should or should not restore the original n-by-n-to-1n multiplication scheme for low digit counts.  
  
I will do all of these and see how it _feels_, restoring both the original low-digit efficiency with compile-time and run-time graduation to Karatsuba as the digits climb up and over about 1,500 or so.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-04-06 11:53:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-814059306  

Cool.  Sounds like a plan Chris.  
  
Just thinking out loud here: is there any way to move towards correctly rounded results?

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-04-06 12:07:22 UTC  
> Updated_at: 2021-04-06 12:08:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-814067472  

> Just thinking out loud here: is there any way to move towards correctly rounded results?  
  
Yes John, you kind of read my mind, as you often do.  
  
There have been a few issues over the years, one is the failure to round on the least significant base-10 digit in multiplication and division because basically the lower half of the multiplication pyramid was always neglected.  
  
If we stick with the (just a bit slower) n-by-n-to-2n multiplciation scheme, the digits for proper rounding are available at each intermediate multiplication. Rounding at add/sub could be done as well.  
  
Another point that was always trouble in the past was the notion of equality. We even had an issue one time on this. At the moment, we handle equality via checking _all_ limbs in `cpp_dec_float`, even guard limbs. This is OK, but not optimal. We might, in fact, consider checking limbs (and the fractional limb) up to and including the most significant base-10 digit.  
  
Numerous, steady improvements over the years have led to a very clean code base for `cpp_dec_float`. These, combined with better language skills and technologies, should make it possible to go about these various _exactness_ issues in a relatively straightforward manner.  
  
I would handle these in a separate sequence of issue(s).  
  
This raises 1 question @jzmaddock, if we try to handle more _exactness_, then I should stick with n-by-n-to-2n multiplciation scheme. Only then can we deal with rounding and exact equality as a consequence. Is this slight performance hit OK? Classic sub-1000 digit multiplication is about 40% slower. or takes about 80% more time (depending how you look at it) because the full pyramid of multiplication is being performed instead of just the half.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2021-04-06 14:02:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-814146011  

Along the way, I would like to note another place to be improving, which I will ultimately address in latr PRs and issues.  
  
While benchmarking 256 sqrt calculations having 10,001 decimal digits, I find that `cpp_dec_float` with Karatsuba is quite a bit faster than `cpp_bin_float`,  with 11.991s (for `cpp_bin_float`) vs. 1.133s (for `cpp_dec_float`).  
  
This is because faster iterative square rooting is done for `cpp_dec_float`. I intend to addrss this discrepancy based on last year`s work in GSoC 2020, but in other PRs and issues.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-04-06 18:55:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-814363269  

>This raises 1 question @jzmaddock, if we try to handle more exactness, then I should stick with n-by-n-to-2n multiplciation scheme. Only then can we deal with rounding and exact equality as a consequence. Is this slight performance hit OK? Classic sub-1000 digit multiplication is about 40% slower. or takes about 80% more time (depending how you look at it) because the full pyramid of multiplication is being performed instead of just the half.  
  
Good question: at present you're using Toombe-Cook and discarding (or rather not calculating) the low order limbs, have I got that right?  Do we only need the low order limbs in the case of a tie?  In which case we could maybe short-circuit their calculation in most cases?  Of course the test may end up being more expensive than just calculating those limbs - sometimes simple wins out.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-04-06 20:53:13 UTC  
> Updated_at: 2021-04-06 20:55:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-814433371  

> Good question: at present you're using Toombe-Cook and discarding (or rather not calculating) the low order limbs, have I got that right? Do we only need the low order limbs in the case of a tie? In which case we could maybe short-circuit their calculation in most cases? Of course the test may end up being more expensive than just calculating those limbs - sometimes simple wins out.  
  
This gets back to the beginning of `cpp_dec_float`. In the original work, I had used multiplication n-by-n-to-1n. So if a representation had 8 limbs, the multiplication would do 1/2(8) * 8 limbs and simply disregard the lower part of the multiplication. This method simply disregarded rounding and used truncation.  
  
In this PR we are implementing Karatsuba multiplication, which is actually Toom-Cook 2-way multiplication. In the Karatsuba multiplication scheme, you need to compute intermediate steps n-by-n-to-2n. In order to complete Karatsuba, I ndeded in this PR to implement the base-case for Karatsuba (now implemented in this PR  as a new n-by-n-to-2n for `cpp_dec_float`). When programming and testin this, I realized that we could use this base-case multiplication for regular small-limb-count multiplication --- _instead_ of the truncated version that we have used for years.  
  
This would allow for rounding, but it will also increase the time of small-digit multiplication operations that has been familiar for years. I could also retain the half-triangle multiplication scheme without rounding for small limb counts. But part of me wants to go for rounding and the performance decrease. This is what you have decided for `cpp_bin_float`.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2021-04-07 09:47:00 UTC  
> Updated_at: 2021-04-07 10:01:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-814774955  

So after 15 hours churning away in the pipeline... Karatsuba and simplifications is all green.  
  
I did some benchmarking:  
  
`boost::math::cbrt`, 101 digits, 1,000,000 cube root calculations:  
- 14.9 s This PR with _full triangle_ of multipllication  
- 9.8 s Boost 1.76 original with _half triangle_ multiplication scheme (no rounding possible).  
   
This PR `sqrt(pi + n)` 5,000 square root calculations with Karatsuba  
- 1.2s for 2,001 digits  
- 3.7s for 4,001 digits  
- 11.3s for 8,001 digits  
- 33.6s for 16,001 digits  
  
The first benchmark shows a slight slowdown in _real-world_ calculations for low digit counts. This is due to the full triangle multiplication scheme.  
  
The second benchmark shows first of all that `cpp_dec_float` can now reach many thousands of digits and also wonderfully displays the expected Order-<img src="https://render.githubusercontent.com/render/math?math=n^1.58"> computational complexity, where we note that <img src="https://render.githubusercontent.com/render/math?math=2^1.58\,\approx\,2.99">.  
  
The PR also cleans up some names in CI, and simplifies numerous areas in `cpp_dec_float` and also drops OSX 7.3 (failing) in CI. Also the more exact dealing of digits in multiplication and carry will pave the way for rounding in future PRs.  
  
All-in-all a nice PR, ready @jzmaddock, @mborland, @NAThompson, @pabristow, optionally and/or whenever time allows, for review.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2021-04-07 10:16:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-814793333  

Additional notes:  
- This PR would bring `cpp_dec_float` much closer to the state of `cpp_bin_float`.  
- Thus preparing the general state for a larger movement I have planned to improve the overall efficiency of both of these in certain low, medium and high digit regions of both.  
- This movement could involve both algebraic operations as well as certain elementary functions such as roots, logarithms, etc.

---

## Review 12 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-07 17:59:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/315#pullrequestreview-630326372  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
  65 |+       // m = 140000; Select[Union@Flatten@Outer[Times, {1, 3, 5}, 2^Range[0, Floor[Log2[m]]]], # < m &]
  66 |+       // In addition, crop the list to begin with 32 or higher.
  67 |+       return ((value <=    32UL) ?    32UL :
```

> Username: jzmaddock  
> Created_at: 2021-04-07 17:59:26 UTC  
> Updated_at: 2021-04-08 10:06:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#discussion_r608875042  

Chris, listen, I'm being seriously nitpicking here, so feel free to ignore this ;)  
  
I was going to suggest this would be better written as a std::lower_bound, but that's not constexpr (at least not in C++11), an alternative would be to manually spell this out as a binary search which would fix the number of comparisons to 5 (I think!) per call.  On the other hand if smaller arguments are more common, then I'm talking nonsense and you should leave it as it is and move on!!


---

## Review 13 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-07 18:03:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/315#pullrequestreview-630330867  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
 586 |+                                           const std::uint32_t  count);
 587 |+ 
 588 |+    #if 0
```

> Username: jzmaddock  
> Created_at: 2021-04-07 18:03:14 UTC  
> Updated_at: 2021-04-08 10:06:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#discussion_r608877434  

Another nit-pick: I understand why this #if 0 is left in for future reference, but a comment to that effect might help us 10 years from now, by which point I fully expect my memory to be long gone south anyway :)


---

## Comment 14

> Username: mborland  
> Created_at: 2021-04-07 18:37:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-815136209  

@ckormanyos I have nothing of value to add/change; this looks very well written.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2021-04-07 18:44:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-815140088  

+1, merge when you're ready Chris!

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2021-04-07 18:50:54 UTC  
> Updated_at: 2021-04-07 18:51:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-815144212  

> merge when you're ready Chris!  
>> looks very well written  
  
Thanks John and Matt. I appreciate the comments and your reviews.  
  
I actually would like to address both of John's review comments plus one of my own before merging to develop.  
  
> better written as a `std::lower_bound`,...  
  
To John's first comment. Yes, indeed. I need this bound both as the static size of an array type as well as a run-time entity for Karatsuba sizing on-the-fly. So I need to either duplicate the list for both `constexpr` as well as binary search modes. or I will figure out something lese. But I do not like spending cycles on the 64 members of the nested if-clauses for each and every single Karatsuba multiplication. So I will definitely address this issue.  
  
My own comment is that I need a `static_assert` that limits the number of decimal digits to the highes Karatsuba amount supported at the time, which will be 131072 * 8 = more than a million decimal digits. This limitation will be lifted upon the implementation of higher-order Toom-Cook schemes and FFT, which will follow in upcoming time epochs.

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2021-04-08 14:06:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-815854076  

Merged to develop. `cpp_dec_float` experiences some minor cleanup and gets Karatsuba multiplication!

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2021-04-09 13:30:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-816683314  

In this closed PR I wonder what's up with exit code 100?  
Does bjam sometimes return 100?  
I've occasoinally seen this or similar:  
  
![grafik](https://user-images.githubusercontent.com/2404721/114187574-6ff5b200-9948-11eb-98f4-676c2de61bb6.png)

---

## Comment 19

> Username: mborland  
> Created_at: 2021-04-09 14:54:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-816741220  

Exit code 100 is a failure from GHA adding repo. It doesn't affect the run because we added in multiple attempts at adding repos.

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2021-04-09 15:53:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/315#issuecomment-816779236  

> Exit code 100 is a failure from GHA adding repo. It doesn't affect the run because we added in multiple attempts at adding repos.  
  
Thank you , Matt! Makes sense.

---
