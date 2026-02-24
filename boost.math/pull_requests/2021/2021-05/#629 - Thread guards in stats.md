# #629 Thread guards in stats [Merged]

> Username: mborland  
> Created at: 2021-05-12 18:39:39 UTC  
> Updated at: 2021-05-20 16:14:59 UTC  
> Merged at: 2021-05-20 10:13:52 UTC  
> Closed at: 2021-05-20 10:13:52 UTC  
> Url: https://github.com/boostorg/math/pull/629  

Allows for use of stats functions in single threaded environments. See issue #621

---

## Comment 1

> Username: mborland  
> Created_at: 2021-05-15 08:39:28 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-841623623  

@jzmaddock I am not quite sure I understand the rash of clang 9 failures on this one. All of the C++20 failures look to be stemming from the standard library, and the quadrature failures are all memory access violations. All the drone tests pass (including ASAN) [except one](https://drone.cpp.al/boostorg/math/209/132/1) which is a failure on Drone's end.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-05-15 12:40:16 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-841653287  

It seems to be *all* the Monte Carlo tests that are failing, @NAThompson any ideas?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-05-15 15:06:29 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-841677088  

Wow! I have *no* clue. It looks like this change doesn't have anything to do with the Monte-Carlo integration. . .  
  
We did have an ASAN build that should've caught this before if it was in user-space, right?  
  
Question: Is there utility in having a single-threaded Monte-Carlo integrator? I personally feel the design of that class is *fundamentally* multithreaded.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-05-15 15:14:41 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-841678208  

I have a dim memory that I disabled sanitizers for the Monte Carlo tests because they take way too long to run (and/or run the machine out of memory).  Might be worth running them locally to see if that gives any clue....  currently struggling with my own (unrelated) thread safety bug yours...

---

## Comment 5

> Username: mborland  
> Created_at: 2021-05-15 16:29:41 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-841688581  

> I have a dim memory that I disabled sanitizers for the Monte Carlo tests because they take way too long to run (and/or run the machine out of memory). Might be worth running them locally to see if that gives any clue.... currently struggling with my own (unrelated) thread safety bug yours...  
  
Locally using Clang-12's ASAN yields clean results on the Monte Carlo tests.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2021-05-16 13:24:21 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-841816749  

Ok, my vote is to *not* disable threads for Monte-Carlo integration. There's little sense in it-the design is fundamentally multithreaded.

---

## Comment 7

> Username: mborland  
> Created_at: 2021-05-16 13:29:19 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-841817356  

> Ok, my vote is to *not* disable threads for Monte-Carlo integration. There's little sense in it-the design is fundamentally multithreaded.  
  
I can revert the change and have it throw an error in single threaded environments.

---

## Comment 8

> Username: mborland  
> Created_at: 2021-05-16 13:47:52 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-841819851  

> > Ok, my vote is to _not_ disable threads for Monte-Carlo integration. There's little sense in it-the design is fundamentally multithreaded.  
>   
> I can revert the change and have it throw an error in single threaded environments.  
  
So after review the thread guards were not added to Monte-Carlo integration in #628. Last time it was touched was over 6 weeks ago by #600. I am still stumped as to why clang-9 is so unhappy.

---

## Comment 9

> Username: NAThompson  
> Created_at: 2021-05-16 14:26:05 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-841824721  

I repeat my unpopular opinion: CI is here to serve us, we don't live to serve CI. If we've run it locally under thread sanitizer, and it's clean, it seems like this is unlikely any users will be bitten by it.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2021-05-17 10:34:17 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-842214371  

I have some inscrutable thread-based failures with clang-9 in Multiprecision also, so I've pushed this: https://github.com/boostorg/math/pull/631 to sanity check the std lib thread support.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2021-05-19 15:33:55 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-844220134  

CI has now been adjusted on develop, @mborland is this now good to go?

---

## Comment 12

> Username: mborland  
> Created_at: 2021-05-19 15:54:09 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-844237808  

> CI has now been adjusted on develop, @mborland is this now good to go?  
  
I expect that this is fine, but I can merge develop to verify.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2021-05-19 16:38:34 UTC  
> Url: https://github.com/boostorg/math/pull/629#issuecomment-844275684  

Nod.  If you don't mind merging develop into this just be on the safe side, that would great!

---
