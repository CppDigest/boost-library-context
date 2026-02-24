# #786 Set BOOST_NO_EXCEPTIONS and BOOST_NO_RTTI for GCC and math in standal… [Merged]

> Username: jzmaddock  
> Created at: 2022-05-12 18:42:52 UTC  
> Updated at: 2024-01-24 21:35:07 UTC  
> Merged at: 2022-05-31 07:43:26 UTC  
> Closed at: 2022-05-31 07:43:26 UTC  
> Url: https://github.com/boostorg/math/pull/786  

…one mode.  
  
Disable everything not needed in error_handling.hpp when no exceptions are available.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-05-12 18:48:09 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1125315774  

@ckormanyos : can you see if this fixes *most* of your standalone + no exceptions issues?  
  
We can very nearly get rid of the all the string and iostream related includes in no-exception handling situations, they are now required from just one place: https://github.com/boostorg/math/blob/926e34c55ce12923e78d13762e5194bab3f7c47f/include/boost/math/policies/error_handling.hpp#L373   
  
Unfortunately, there's no way we can tell at preprocessor time whether that procedure will be required.  However, if it's not instantiated, none of the string or iostream code is ever used or instantiated.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-05-12 19:26:17 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1125346030  

Hi John (@jzmaddock) this does not entirely eliminate the problem(s).  
  
I posted some sample code in the original post #785. If I play around, I can probably get some other tiny errors. But at the moment, i think Bernoulli might be throwing and is not caught in the expected mechanism.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-05-12 19:29:23 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1125348416  

Ahhh... Last but not least, this guy throws [here](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/special_functions/detail/bernoulli_details.hpp#L191).  
  
Your fix is _almost_ complete for the moment. i will check for some other throwing activities in the general code base.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2022-05-12 19:32:27 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1125350853  

It seems like we have a couple of rogue throw potentials --- also a handful in my own creation of cstdfloat.  
  
How should we deal with these?  
  
![grafik](https://user-images.githubusercontent.com/2404721/168154033-688a2af0-89a6-41dc-8281-26cca319e8e6.png)

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2022-05-13 17:24:47 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1126279261  

I think I can see how to fix up bernoulli...

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2022-05-13 18:24:46 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1126322168  

> fix up bernoulli  
  
Yes. Many thanks John. Commit a6db2d19b7e96f802af751b039090d184343b3b5 is confirmed to fix my test case.

---

## Review 7 [Commented]

> Username: ckormanyos  
> Created_at: 2022-05-13 18:56:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/786#pullrequestreview-972675328  

📁 include/boost/math/special_functions/detail/bernoulli_details.hpp

```diff
 189 |       if(n > m_capacity)
 190 |       {
 191 |+ #ifndef BOOST_NO_EXCEPTIONS
```

> Username: ckormanyos  
> Created_at: 2022-05-13 18:56:10 UTC  
> Updated_at: 2022-05-13 18:56:11 UTC  
> Url: https://github.com/boostorg/math/pull/786#discussion_r872696822  

I'm not an expert, but just curious here... Why is this unexpected event handled differently than the ones below that go through _policies_? Is there no such fitting policy for this action?


---

## Comment 8

> Username: ckormanyos  
> Created_at: 2022-05-13 18:57:53 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1126356000  

For the next trimesters or so, this is more than I had hoped for, upon running green...  
  
Thank you  John!

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2022-05-14 07:54:30 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1126665967  

Hi John (@jzmaddock), based on [this minute compiler phenomenon](https://github.com/ckormanyos/wide-decimal/runs/6433159816?check_suite_focus=true#step:5:26) in my CI on an unrelated project on this branch, I took the liberty to add a tiny change.  
  
Please give me your feedback on this at your convenience.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2022-05-14 08:15:09 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1126668934  

On another thought, and I'm not sure if I'd like to push for this right at this moment, but _yet_ _another_ CI job _could_ ensure that spec-fun runs with `-fno-exceptions` and `-fno-rtti` on at least one of each modern GCC/clang.  
  
I think spec-fun is where some of the exception-free clients will see some benefit, as these folks might (unknowingly) be on the metal or could eventually use such a feature to gain savings.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2022-05-15 08:04:34 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1126882421  

>On another thought, and I'm not sure if I'd like to push for this right at this moment, but yet another CI job could ensure that spec-fun runs with -fno-exceptions and -fno-rtti on at least one of each modern GCC/clang.  
  
That was my thought too, unfortunately, running the tests locally shows that Boost.Test doesn't support -fno-exceptions so *everything* fails to build :(

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2022-05-15 08:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1126883718  

> Boost.Test doesn't support `-fno-exceptions` so everything fails to build  
  
Thanks for looking into this John!  
  
Ah yes, now that you mention it, that makes perfect sense.  
  
I'm totally happy with the level reached so far and think if it's green, then it's good to go.  
  
I consider us to be on a pathway toward reduced dependencies and flexibility. We might find a few things to improve (such in this PR here). Then we improve them. Maybe later, we find a few more. Maybe further down the road, we spend some time to build up a light-weight test environment, maybe not.  
  
To re-iterate, I am really happy with thisw PR in its current state. I already ran my CI (doing a bunch of `tgamma`-like stuff) in another challenging, unrelated project on this branch. I ran with `-fno-exceptions` and `-fno-rtti` on GCC 4 through 11 and a bunch of clang versions and everything worked perfectly!

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2022-05-15 17:39:57 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1126984772  

Wow! Even more awesomeness. Thanks John!

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2022-05-16 06:49:38 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1127288225  

Hi John. I think one try-catch clause was missed in spec-fun no_eh in places like [here](https://github.com/boostorg/math/runs/6442199194?check_suite_focus=true#step:18:335).  
  
The failures on Jammy look suspiciously like timeouts to me, although I'm not exactly sure what error code 143 means.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2022-05-16 07:14:06 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1127310705  

The problem in spec fun on no-eh is [here](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/special_functions/chebyshev_transform.hpp#L130), I believe.  
  
I don't know what you'll find out on the failures on GCC. I have some very preliminary reports (from other projects) on what I might consider to be spurious behaviors in GCC12 on built-in memory operations, but that's super preliminary and not verified.

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2022-05-16 11:23:39 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1127549067  

Github is timing out whenever I try to access the logs at present, I'll take a look later...

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2022-05-16 11:25:26 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1127550746  

> Github is timing out whenever I try to access the logs at present, I'll take a look later  
  
Yes. The pink unicorn is bucking and rearing its head

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2022-05-16 17:26:23 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1127940185  

Still can't access the logs, so pushed a couple of speculative commits based on what I did manage to load!

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2022-05-17 05:16:38 UTC  
> Updated_at: 2022-05-17 05:18:17 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1128421250  

Well, also this morning I'm still getting the pink Unicorn on log viewing.  
  
Maybe it'll come to its senses later, but it's kind of hard to see what's going on without log viewing.

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2022-05-24 15:02:57 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1136046085  

Hi John, wonderful progress on this.  
  
I reeally think this one is getting close. On this particular run, however, we get a hitch on no-eh [here](https://github.com/boostorg/math/runs/6572342065?check_suite_focus=true#step:18:839).

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2022-05-24 15:49:41 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1136100527  

> I reeally think this one is getting close. On this particular run, however, we get a hitch on no-eh [here](https://github.com/boostorg/math/runs/6572342065?check_suite_focus=true#step:18:839).  
  
Grrr, forgot to check those locally... at least this time we can actually see the logs!  Old run cancelled and fix pushed.

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2022-05-30 05:02:15 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1140699720  

This is cool.  
  
It seems _almost_ green. There does, however, seem to be an actual failure in a test case on Chatterjee in Drone on Ubuntu clang++-10 with language standard `-std=gnu++17`. It might be a tolerance, or the problem might be not yet known.

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2022-05-30 17:43:11 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1141376564  

> It seems almost green. There does, however, seem to be an actual failure in a test case on Chatterjee in Drone on Ubuntu clang++-10 with language standard -std=gnu++17. It might be a tolerance, or the problem might be not yet known.  
  
It's a tolerance thing: tolerance was 0.01 and the error found was 0.0103.  I suspect @NAThompson and @mborland missed that one because we have one consistently failing test (an autodiff sanitizer test runs the machine out of memory I suspect), I've disabled that one, and also removed a few redundant Github tests - it doesn't cut down the testing load by much to be honest - I can't decide whether we should cull a few more or not - it feels like we should, but I worry about missing something...!

---

## Comment 24

> Username: ckormanyos  
> Created_at: 2022-05-30 20:34:59 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1141462982  

> It's a tolerance thing: tolerance was 0.01 and the error found was 0.0103. I suspect @NAThompson and @mborland missed that one because we have one consistently failing test (an autodiff sanitizer test runs the machine out of memory I suspect), I've disabled that one, and also removed a few redundant Github tests - it doesn't cut down the testing load by much to be honest   
  
Nice!  
  
> - I can't decide whether we should cull a few more or not - it feels like we should, but I worry about missing something...!  
  
Matt (@mborland) recently expressed interest in this topic.  
  
One thing on GHA is that you can now selectively re-run either failed tests only or all tests entirely. I've used this selective re-run feature on Multiprecision successfully to go green. It does not cut down any testing time. Also the fail we mention here wasn't on GHA but Drone. Anyway, I thought I'd mention that if it was not known.  
  
Thank you very much John (@jzmaddock) for driving onward in this PR!

---

## Comment 25

> Username: mborland  
> Created_at: 2022-05-30 20:45:11 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1141467442  

> It's a tolerance thing: tolerance was 0.01 and the error found was 0.0103. I suspect @NAThompson and @mborland missed that one   
  
It is safe to bump up the tolerance. It's shooting for correlation on random numbers compared to an approximate published value.   
  
> I can't decide whether we should cull a few more or not - it feels like we should, but I worry about missing something...!  
  
Can we offload more testing to drone? It seems like those runs generally finish in an hour right now as opposed to the 6+ it takes GHA.

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2022-05-31 11:55:31 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1142036810  

@mborland I'll look into shifting some load onto drone later.

---

## Comment 27

> Username: ckormanyos  
> Created_at: 2022-05-31 12:05:53 UTC  
> Updated_at: 2022-05-31 12:06:38 UTC  
> Url: https://github.com/boostorg/math/pull/786#issuecomment-1142046587  

Thank you John (@jzmaddock) for sticking with this mammoth PR. It entailed many remarkably detailed and difficult nuances.  
  
I've already checked this branch out on the metal and in a bunch of unrelated CI tasks on other projects that dis-allow exception handling. It works fantastically for spec-fun-stuff.  
  
If anything else arises, we can handle it in the next trimester. But this looks really great, light and clean for no-exception usage!

---
