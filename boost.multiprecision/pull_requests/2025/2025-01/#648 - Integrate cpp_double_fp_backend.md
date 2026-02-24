# #648 Integrate cpp_double_fp_backend [Closed]

> Username: ckormanyos  
> Created at: 2025-01-06 17:35:48 UTC  
> Updated at: 2025-06-26 04:25:08 UTC  
> Closed at: 2025-06-19 20:43:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-01-06 17:36:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2573589480  

Retry due to confusion:  
  
This PR attempts to add `cpp_double_fp_backend<T>`.  
  
There are still a few TODOs (a very few) in the original GSoC repo.  
  
I've started this PR in the _real_ Boost nonetheless in the hopes that we might make 1.88 (or later 1.89). There's not much happenning in Multiprecision so it does not really matter if this PR is open for 5 weeks or 5 months.  
  
I also want to gain experience with the full CI  
  
Cc: @jzmaddock and @sinandredemption and @cosurgi and @mborland

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-01-07 21:45:29 UTC  
> Updated_at: 2025-01-07 21:46:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2576282633  

OK. This thing is going well.  
  
The only failures are on drone now on x390 and Apple 64-bit when exercising the arithmetic-tests. The cause is due to some confusion with `<limits>`, which should be easy enough to deal with. So basically the hard parts are done on `cpp_double_fp_backend<T>`.  
  
I'll figure out how to deal with those non-x86_64 architectures (which I'm happy to have in CI) and finish off this thing.  
  
This is getting close rather sooner than later.  
  
Cc: @sinandredemption and @jzmaddock and @mborland and @cosurgi

---

## Comment 3

> Username: cosurgi  
> Created_at: 2025-01-07 21:49:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2576289912  

This is seriously awesome. Thank you so much for your great work. I will try to find some time to look at this more closely.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2025-01-07 21:58:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2576301722  

> This is seriously awesome. Thank you so much for your great work. I will try to find some time to look at this more closely.  
  
Thank you Janek (@cosurgi). I squeezed down the run-time even further, especially on mul/div (at least in my very basic perf tests) and the whole thing is even faster now.  
  
Indeed, I'll be interested to hear of your findings. And if this goes well, we can rather easily go for `quad` as time allows (which was the original OP a few years ago)!

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2025-01-08 18:59:24 UTC  
> Updated_at: 2025-06-19 20:43:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2578408453  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/648?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 100.0%. Comparing base [(`6df8e19`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/6df8e19f00632ef1d00ec5cc0cd813a22a4df201?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a3d7e63`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/a3d7e63f95d408194d3a00272eca7d222eb89437?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/648/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/648?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #648      +/-   ##  
===========================================  
+ Coverage     94.1%   100.0%    +6.0%       
===========================================  
  Files          280        1     -279       
  Lines        29074        6   -29068       
===========================================  
- Hits         27347        6   -27341       
+ Misses        1727        0    -1727       
```  
  
[see 279 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/648/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/648?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/648?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6df8e19...a3d7e63](https://app.codecov.io/gh/boostorg/multiprecision/pull/648?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-01-08 19:25:12 UTC  
> Updated_at: 2025-01-08 19:26:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2578460042  

OK @sinandredemption and @cosurgi and @jzmaddock the `cpp_double_fp_backend` has gone green. There are some rudimentary docs as well.  
  
I still need to actually _build_ and push the built-docs. But I might do this _after_ the upcoming review of another, different candidate Boost library, coming right up next week.  
  
Um from my subjective standpoint, this backend is ready to go out and run wild. Let's see how Janek's first hard-real-world test comes out, good, neutral or disaster and then see where we want to go with this backend.  
  
I do not have any real huge open issues. There are a few subtle checks I/we could add for overflow catch in add/sub. But at the moment I am hesitant to slow down the backend. It is at the moment 3x faster than the corresponding dec-float backend in my Mandlebrot bench.  
  
And I'm excited to see how this thing faces off with other backends in Janek's Yade simulation.  
  
Let's see how it goes...  
  
Cc: @mborland and @NAThompson

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2025-01-11 17:46:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585354845  

See also https://github.com/BoostGSoC21/multiprecision/issues/181

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2025-01-11 21:50:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585456615  

OK this thing is green again with the improvements on performance, especially the `str()` method.  
  
I will add the updated performance data to the documentation and add some motivational words. These words will also be cautionary and clearly state that the most powerful benefit of this backend at the moment is for pure numerical calculations and that the internal string-streaming operations are not (yet) optimal.  
  
So I need to:  
  
- Add perf data to the docs.  
- Build the docs.  
  
Then this thing is good to go.  
  
Cc: @sinandredemption and @cosurgi and @jzmaddock

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2025-01-11 21:59:01 UTC  
> Updated_at: 2025-01-12 18:25:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585458606  

The coverage on `cpp_double_fp.hpp` could be improved. But this should not impede this PR in my opinion at the moment.  
  
See also https://github.com/BoostGSoC21/multiprecision/issues/182

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2025-01-12 19:54:05 UTC  
> Updated_at: 2025-01-12 19:56:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585895427  

> The coverage on `cpp_double_fp.hpp` could be improved.  
  
So the coverage is, let's say, adequate and is reaching the ${\approx}94\\%$ for the overall project. A few things are found such as the internal function `pown` is not called (at least in coverage).  
  
Also the `float128` stuff is not included in the coverage report, although this is being called. I guess I'd need another job (with `-std=gnu++XX`) in the coverage YAML runs in order to hit these lines _and_ get them reported in coverage. This would be simple enough but let's see if I get to it (or postpone it, which is also fine).

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2025-01-12 20:36:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585908060  

>> The coverage on cpp_double_fp.hpp could be improved.  
  
> So the coverage is, let's say, adequate  
  
I'm cool with it at this juncture. The report has 52 lines missing in coverage from `cpp_double_fp.hpp`. I'm satisfied with that for the first cut.  
  
Next stop, add perf-info, build docs and move forward.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2025-01-12 20:39:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585908865  

> I'm cool with it at this juncture. The report has 52 lines missing in coverage from cpp_double_fp.hpp. I'm satisfied with that for the first cut.  
  
It ought to be simple enough to pep the entire project up to 95-plus coverage when dealing with a few more edge cases in the future. but I'll leave that quality goal for another tmie, ...

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2025-01-12 20:45:23 UTC  
> Updated_at: 2025-01-12 20:45:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585910442  

Hi @jzmaddock and @mborland.  
  
We worked a bit on this one. I'm happy with the functionality, performance and quality. I'm also moderately pleased with the testing, the coverage and the overall correctness and behavior of this backend.  
  
On last thing --- I need to build and check in the docs. But the double-double (or as I so lovingly call it, the double-trouble backend) is good to go.  
  
I'm ready with this thing. Are there any significant comments, reservations or change requests before I doc this thing and push to develop?  
  
This might take a week or 2 in order to finish, during which time this comment phase is fully open.  
  
Cc: @sinandredemption and @cosurgi and @NAThompson

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2025-01-12 20:59:15 UTC  
> Updated_at: 2025-01-12 21:00:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585914270  

This is part one of https://github.com/boostorg/multiprecision/issues/184  
  
And my favorite benchmark (and I'm using this thing already for high-speed fractal iterations).  
  
It gives me the pure algebra power I need (and I can harldy wait to get it on a GPU):  
  
### Pure Algebra Timing Report  
  
- Full classic Mandelbrot image $2048{\times}2048$ pixels at 32 decimal digit precision.  
- `cpp_double_double` versus `cpp_dec_float<32>`  
- $2000$ iterations, half-width $1.35$, centered around the point $(0.0, -0.75i)$.  
- Using 15 cores multithreading on Intel Core-i9.  
  
| number type                                    | time (s)  | factor |  
| -------------------------------------- | --------- | -------|  
| `cpp_double_double`                       | 17          |[1]  
| `cpp_dec_float<32>`                       | 52          |3|  
  
And the picture is, ... beautiful!  
  
![mandelbrot_MANDELBROT_01_FULL](https://github.com/user-attachments/assets/3eb27ef8-731a-4f26-8e59-b9dbc4698b5c)

---

## Comment 15

> Username: cosurgi  
> Created_at: 2025-01-12 21:01:58 UTC  
> Updated_at: 2025-01-12 21:11:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585915175  

How about Mandelbrot in `float128` :)

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2025-01-12 21:05:11 UTC  
> Updated_at: 2025-01-12 21:05:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585916116  

> How about Mandelbrot in float128 :)  
  
Good question Janek (@cosurgi)! I'll let you know. That thing ran in Win*. But since elementary add/sub/mul/div were always faster on double-trouble I don't expect any problems. But the proof is in the pudding. I'll run on *nix and tll you in a day or so.

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2025-01-12 21:16:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585919468  

>> How about Mandelbrot in float128 :)  
  
> Hi Janek, the suspense was killing me, ...  
  
double-trouble 12s  
float128 on WSL2 26s  
  
![image](https://github.com/user-attachments/assets/303d5579-36df-4003-a87a-a9f6c11a46ab)  
  
![image](https://github.com/user-attachments/assets/c4e7c8ff-db0a-42be-83c4-04ff564647ad)

---

## Comment 18

> Username: cosurgi  
> Created_at: 2025-01-12 21:18:10 UTC  
> Updated_at: 2025-01-12 21:18:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585919911  

> double-trouble 12s  
> float128 on WSL2 26s  
  
Awesome!

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2025-01-12 21:20:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585920502  

We had a stumble on yade. But this thing actually performs on raw algebra. We just need to work out the kinks. I did not even optimize it fully yet regarding single-limb, int, etc. operations. So it can get better.  
  
When we started this thing, I was skeptical. Now I'm convinced. If you have a limited exponent range and you just need a few more digits, then double-fp-backend might be a good choice.  
  
I'm excited about quad.

---

## Comment 20

> Username: cosurgi  
> Created_at: 2025-01-12 21:21:17 UTC  
> Updated_at: 2025-01-12 21:21:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585920848  

> I'm excited about quad.  
  
Me too! :)

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2025-01-12 21:22:20 UTC  
> Updated_at: 2025-01-12 21:22:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585921101  

But we still need to face yade and see if there are any issues there needing to be addressed. We can do this as soon as you work out reduction of string-streaming.  
  
But that is a battle for another day...

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2025-01-14 21:51:21 UTC  
> Updated_at: 2025-01-14 21:53:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2591178191  

OK so `cpp_double_fp.hpp` is up to almost $99\\%$ line coverage. All tests are passing. Perf on algebra is good. All of CI is green.  
  
There are $8$ missing cover lines in the header. I might go for $3$ lines or so. The rest are actually open issues in my mind.  
  
So final steps are:  
  
- Finish coverage.  
- Build and check in docs.  
  
Cc: @sinandredemption and @cosurgi and @jzmaddock

---

## Comment 23

> Username: cosurgi  
> Created_at: 2025-01-14 22:49:47 UTC  
> Updated_at: 2025-01-15 00:20:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2591267123  

I have a preliminary version of yade where I eliminate string conversions, the CI is cycling here: https://gitlab.com/yade-dev/trunk/-/merge_requests/1070  
  
Tomorrow I have to focus on something else, but in a couple of days I should be able to run a benchmark of `cpp_double_*` types and see what really is the speedup with this new `cpp_double_fp_backend`.  
  
EDIT: I see now that more work is needed. I reduced the `rd_string` call count from 233480 to 104783, but I think it can be reduced much more, with much bigger changes.

---

## Comment 24

> Username: ckormanyos  
> Created_at: 2025-01-15 09:52:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2592164707  

> run a benchmark of `cpp_double_*` types and see what really is the speedup with this new `cpp_double_fp_backend`.  
  
At the moment, I am relatively confident in pure algebraic speed. There are a few open issues on some elementary functions. And read/write string is known inefficient.  
  
So all-in-all the `cpp_double_fp_backend` is ready for something like a _first_ _release_. But it might take a while to ensure that every single part of the backend is efficient. And I expect this will happen in a sequence of fixes and iterations over the next few months.  
  
Let's see where we sand now. Thankis Janek (@cosurgi).

---

## Comment 25

> Username: ckormanyos  
> Created_at: 2025-01-15 20:18:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2593852489  

OK I'm done with the technical development of `cpp_double_fp_backend`. And I am satisfied with this first draft.  
  
Summary:  
  
- Running in tests and CI and locally for `cpp_double_fp_backend`  
- Performance very good for all of algebra and most special functions.  
- Missing only 4 lines of coverage so above $99\\%$ cover.  
  
Just need to finish the docs.  
  
Cc: @sinandredemption and @cosurgi and @jzmaddock and @mborland

---

## Comment 26

> Username: cosurgi  
> Created_at: 2025-01-16 17:04:18 UTC  
> Updated_at: 2025-01-16 17:13:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2596258753  

Hi Chris (@ckormanyos) this is very hot off the press. I have just managed to reduce the number of `rd_string` calls from 233480 to 48 and `str` calls from 194842 to 437 in the `yade -n --quickperformance -j 1` benchmark!  
  
I cannot run the timing benchmarks yet, because I need to clean up the code and remove all these `std::cerr << __PRETTY_FUNCTION__ << "\n";`  everywhere ;) They slow down the calculations!  
  
Now if we manage to add `pow` for integer powers we will be good to go!

---

## Comment 27

> Username: ckormanyos  
> Created_at: 2025-01-16 17:11:50 UTC  
> Updated_at: 2025-01-16 17:16:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2596274961  

> Hi Chris (@ckormanyos) this is very hot off the press. I have just managed to reduce the number of `rd_string` calls from 233480 to 48 and `str` calls from 194842 to 437 in the `./examples/yade -n --quickperformance -j 1` benchmark!  
  
This is great. Nice work.  
  
> Now if we manage to add `pow` for integer powers we will be good to go!  
  
I have not done that, but I verified that we go to a somewhat efficient calculation in our existing collection of default functions. If this ends up being one of the"functions-to-speed-up", then I can do that fast.  
  
Now, we might still face some quirky issue, and the _proof_ will be in your final bench run. If we are speedy, then good. If, for some other reason, we still face slow downs then we even now have two wins:  
  
- YADE is going to get better  
- And if we still need to squeeze more `cpp_double_fp` functions, then we can find them and push their limits down.  
  
So no matter what actual numbers you get, your work has put us in a stronger position. Let's keep going!  
  
Cc: @sinandredemption and @jzmaddock

---

## Comment 28

> Username: cosurgi  
> Created_at: 2025-01-16 17:26:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2596303952  

Wow, with last single small change I reduced the `rd_string` calls to 2 !

---

## Comment 29

> Username: cosurgi  
> Created_at: 2025-01-16 21:09:42 UTC  
> Updated_at: 2025-01-16 21:10:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2596893309  

> > Now if we manage to add `pow` for integer powers we will be good to go!  
  
> I have not done that, but I verified that we go to a somewhat efficient calculation in our existing collection of default functions. If this ends up being one of the"functions-to-speed-up", then I can do that fast.  
  
Hi Chris (@ckormanyos), what happens if you replace `z = z*z + c;` with `z = pow(z,2) + c;` in your Mandelbrot benchmark?

---

## Comment 30

> Username: ckormanyos  
> Created_at: 2025-01-16 21:15:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2596901813  

> Hi Chris (@ckormanyos), what happens if you replace `z = z*z + c;` with `z = pow(z,2) + c;` in your Mandelbrot benchmark?  
  
It ruins the performance completely and entirely. What a great question Janek. It took so long that I am still waiting for the timing result. i had real/imag components separated. See the pic below.  
  
In summary, the pow function killed performance on that particular benchmark.  
  
I went from 17 seconds to 170 seconds, a factor of 10  
  
![image](https://github.com/user-attachments/assets/a993f7d2-ffc7-4937-acab-493112ffae5f)

---

## Comment 31

> Username: ckormanyos  
> Created_at: 2025-01-16 21:16:17 UTC  
> Updated_at: 2025-01-16 21:17:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2596903841  

Nightmare timing up by a factor of 10.  
  
Oops, it is time to hand-optimize `pow(x, n)` where $n$ is an integer.  
  
![image](https://github.com/user-attachments/assets/046c313f-1dc9-42ed-9d6e-1230696643c5)

---

## Comment 32

> Username: cosurgi  
> Created_at: 2025-01-16 21:47:41 UTC  
> Updated_at: 2025-01-17 09:03:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2596964680  

Whew. That's good news for me actually, because yade uses `pow(arg, 2)` 436138 times in the calculations. And I have removed calls to `rd_string` entirely. And I got this benchmark result (more "iter/sec" means better result):  
  
| type                    | calculation speed     | factor   |  
| ----------------------- | --------------------- | -------- |  
| float128                | 159.4864 iter/sec     | 1        |  
| cpp_double_double       | 31.3289 iter/sec      | 5.09     |  
  
Meaning that in yade `float128` is still 5 times faster than `cpp_double_double`.  
  
Before removing calls to `rd_string` it was like this:  
  
| type                    | calculation speed     | factor   | commit   |  
| ----------------------- | --------------------- | -------- | -------- |  
| float128                | 145.3411 iter/sec     | 1        |          |  
| cpp_double_double       | 30.4207 iter/sec      | 4.77     | 9f346580 |  
  
So you may notice that `float128` performance increased by a factor of 1.1 (10% faster) thanks to removing string streaming. And `cpp_double_double` is also faster, but only a tiny bit: by a factor of 1.03 (3% faster). While the comparison between the two actually got worse: from 4.77 to 5.09. Meaning that `float128` benefitted more from the removal of string streaming than `cpp_double_double`.

---

## Comment 33

> Username: cosurgi  
> Created_at: 2025-01-16 22:08:45 UTC  
> Updated_at: 2025-01-16 22:25:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2597009776  

I am not sure if on that screenshot the lines 140 and 141 are correct? You have `zr2 = pow(zr, zr)`,  
shouldn't it be:`zr2 = pow(zr, 2)` ? Or something like this, but not raising $zr^{zr}$.

---

## Comment 34

> Username: ckormanyos  
> Created_at: 2025-01-17 07:28:27 UTC  
> Updated_at: 2025-01-17 07:29:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2597600273  

> I am not sure if on that screenshot the lines 140 and 141 are correct? You have `zr2 = pow(zr, zr)`,  
shouldn't it be: `zr2 = pow(zr, 2)`?   
  
You are right Janek. That was a silly, late evening, hurried blunder.  
  
When I used the proper `pow(zr, 2)`, the timing was worse $24s$ compared to $17s$, but not so bad as the previous report.  
  
![image](https://github.com/user-attachments/assets/7d136c91-23d0-4369-96d2-19fde17598f1)  
  
![image](https://github.com/user-attachments/assets/60447da1-f503-416c-b262-3ee0cc52c02e)

---

## Comment 35

> Username: jzmaddock  
> Created_at: 2025-01-17 09:28:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2597800585  

Just curious, did we not optimize the default pow function for integer exponents?  
  
Also just FYI the boost::math::pow<N>(x) function is designed to optimise exactly this case: a power with a constant integer exponent.  So far as I know there is no way within the language to detect that pow(T, int) is being called with an integer literal?

---

## Comment 36

> Username: ckormanyos  
> Created_at: 2025-01-17 10:47:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2598014096  

> Just curious, did we not optimize the default pow function for integer exponents?  
  
Yes John, you are right. The generic collection of functions in Multiprecision DOES include specializations of `eval_pow` for pure integral powers.  
  
I am experimenting with a local version, but I am not able to get significantly faster than the default version in Multiprecision, maybe only $10-20\\%$ faster.  
  
At the moment, I am not able to see any more clear bottlenecks in the overall performance of `cpp_double_fp_backend`.

---

## Comment 37

> Username: jzmaddock  
> Created_at: 2025-01-17 14:43:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2598519634  

@ckormanyos does this PR improve power performance at all: https://github.com/boostorg/multiprecision/pull/649 ?

---

## Comment 38

> Username: cosurgi  
> Created_at: 2025-01-17 15:45:26 UTC  
> Updated_at: 2025-01-17 16:59:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2598645680  

Chris (@ckormanyos) can you share your Mandelbrot benchmark code? I want to make sure that I can reproduce your results. Because if I don't, then we know it's not a problem with `cpp_double_fp` but with my local configuration.

---

## Comment 39

> Username: cosurgi  
> Created_at: 2025-01-17 16:56:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2598787746  

Chris (@ckormanyos) in [this post](https://github.com/boostorg/multiprecision/pull/648#issuecomment-2585919468) with the Mandelbrot benchmark which version of g++ and optimization flags (`-O3`, `-Ofast` ?) did you use to compare `cpp_double_double` with `float128` ?

---

## Comment 40

> Username: ckormanyos  
> Created_at: 2025-01-17 17:07:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2598806309  

> the Mandelbrot benchmark  
  
See also: https://github.com/BoostGSoC21/multiprecision/issues/190  
  
Hi Janek (@cosurgi), I have made a dedicated issue for this discussion. In that issue, I will provide the benchmark code and, yes, it does offer the ability to compare bin-float, dec-float, float128 and double-double.  
  
Give me a day or so to prepare a branch of the Mandelbrot for your dedicated use.  
  
Cc: @jzmaddock and @sinandredemption

---

## Comment 41

> Username: ckormanyos  
> Created_at: 2025-01-17 17:14:26 UTC  
> Updated_at: 2025-01-17 17:17:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2598822895  

> does this PR improve power performance at all  
  
Hi John (@jzmaddock). In a word, yes. Treating small powers in that super-fast way is something we should probably do.  
  
Another thing I have been playing around with is a more subtle issue. In my recent pushes here, I have introduced a concept called `mul_unchecked`. And this just cycled green.  
  
In `mul_unchecked` I skip the prologue to multiplication which checks for NaN, infinity, zero and the like. Sort of making a pure multiplication that is separate from the `eval_mul` operation.  
  
As it turns out, the floating-point-class checks actually do slow down these tiny, tiny backends significantly. We also found this to be relevant for the work in decimal. So a bit down the evolutionary road I will also be separating the work from the safety of mul/div operations as well.  
  
So if you have already checked edge cases in a function like `pow` or `exp` or similar, you can squeeze away further checks in mul/div in that function's implementation.  
  
As for your changes there, I think they definitely help all of multiprecision, but I still _might_ end up specializing $x^n$ for the double-float backend if that squeezes out $5\\%$ or more, as it seems to in my recent studies.  
  
Cc: @cosurgi

---

## Comment 42

> Username: cosurgi  
> Created_at: 2025-01-17 22:58:54 UTC  
> Updated_at: 2025-01-18 00:07:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2599344118  

I posted some latest YADE benchmark results in https://github.com/BoostGSoC21/multiprecision/issues/190 , suddenly it starts to look good with clang.  
(initially I posted this here, but then I moved this post over there)

---

## Comment 43

> Username: ckormanyos  
> Created_at: 2025-01-18 14:02:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2599728735  

Note to self: TODO Hit the edge-cases of the new `eval_pow` method.

---

## Comment 44

> Username: ckormanyos  
> Created_at: 2025-01-18 18:03:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2599811427  

Performance of algebraic functions re-affirmed in https://github.com/BoostGSoC21/multiprecision/issues/190

---

## Comment 45

> Username: cosurgi  
> Created_at: 2025-01-18 21:19:54 UTC  
> Updated_at: 2025-01-18 21:34:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2599985285  

OK, so the bad performance mystery was [solved](https://github.com/BoostGSoC21/multiprecision/issues/190#issuecomment-2599763610) and I did  benchmarks of [YADE software](https://yade-dem.org/doc/) `yade -n --quickperformance -j 4` on a quite recent CPU Intel i7-14700KF and the results are good. Some are interesting. We can definitely mark the performance problem of the `cpp_double_fp_backend` as solved. Now only the compiler developers will have something to talk about :)  
  
Here are the results:  
  
### `cpp_double_double`  
  
| type                                     | calculation speed     | factor   |  
| ---------------------------------------- | --------------------- | -------- |  
| `cpp_double_double`       g++ 12.2       |   449.15 iter/sec     |   1      |  
| `float128`                g++ 12.2       |   263.15 iter/sec     |   1.70   |  
| `cpp_bin_float<32>`       g++ 12.2       |   211.81 iter/sec     |   2.12   |  
| `cpp_dec_float<31>`       g++ 12.2       |    78.15 iter/sec     |   5.74   |  
| `mpfr_float_backend<31>`  g++ 12.2       |    51.01 iter/sec     |   8.80   |  
  
Here we can see that `cpp_double_double` beats everyone else by over a factor of two.  
  
### `cpp_double_long_double`  
  
| type                                     | calculation speed     | factor   |  
| -----------------------                  | --------------------- | -------- |  
| `cpp_bin_float<39>`       g++ 12.2       |   122.55 iter/sec     |   1      |  
| `cpp_double_long_double`  clang++ 19.1.4 |   108.79 iter/sec     |   1.12   |  
| `cpp_bin_float<39>`       clang++ 19.1.4 |   102.19 iter/sec     |   1.20   |  
| `cpp_dec_float<39>`       g++ 12.2       |    71.42 iter/sec     |   1.71   |  
| `mpfr_float_backend<39>`  g++ 12.2       |    45.75 iter/sec     |   2.67   |  
| `cpp_double_long_double`  g++ 12.2       |    14.97 iter/sec     |   8.18   |  
  
Here we can see that `cpp_double_long_double` performs very good. But the compiler developers will have a mystery to solve: `cpp_bin_float<39>`       g++ 12.2 is faster than `cpp_double_long_double`  clang++ 19.1.4 by just a little, which in turn is faster than `cpp_double_long_double`  g++ 12.2 by a factor of 8.  
  
### `cpp_double_float128`  
  
| type                                     | calculation speed     | factor   |  
| -----------------------                  | --------------------- | -------- |  
| `cpp_bin_float<67>`   g++ 12.2                     |   118.43 iter/sec     |   1      |  
| `mpfr_float_backend<67>`      g++ 12.2             |    43.34 iter/sec     |   2.73   |  
| `cpp_dec_float<67>`          g++ 12.2              |    40.09 iter/sec     |   2.95   |  
| `cpp_double_float128`      g++ 12.2                |    14.99 iter/sec     |   7.90   |  
  
Here we can see that `cpp_double_float128` has a lot of potential to beat `cpp_bin_float<67>` once the g++ developers sort out the problems with `cpp_double_long_double`  g++ 12.2. The increase in performance should be about by a factor of 8 :)  
  
So all is good. I think we can merge this branch once documentation and other small TODOs are complete.

---

## Comment 46

> Username: ckormanyos  
> Created_at: 2025-01-19 08:10:15 UTC  
> Updated_at: 2025-01-19 08:12:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2600750772  

> We can definitely mark the performance problem of the `cpp_double_fp_backend` as solved.  
  
Thank you Janek (@cosurgi) that was a big effort, and it really provided a lot of information and clarity.  
  
Some of the results on `cpp_double_long_double`, where `long double` is 80-bit, 10-byte in width are interesting. That hardware version of the 10-byte floating-point representation is running on the legendary (modernized) versions of the i387 FPU, the hardware that really put 10-byte floating-point on the map.  
  
The newer i7 processors have extremely powerful 64-bit floating-point hardware operations, and it seems like these are being very well supported nowdays in hardware and software.  
  
Down the road I will be doing some non-x86_64 measurements on M1 and/or M2 and a few embedded bare-metal controllers like  an ARM(R) Cortex(R) M7, having double-precision floating-point FPU support.  
  
All-in-all I'm somewhat surprised at how fast `cpp_double_double` ended up in certain harware/software configurations. As mentioned in previous posts, this backend (and of course that type specifically) have lots of room for optimization improvement.  
  
I'm happy _enough_  with it to make a first release out of this state.  
  
Cc: @sinandredemption and @jzmaddock

---

## Comment 47

> Username: jzmaddock  
> Created_at: 2025-01-19 09:08:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2600770222  

There *might* be one more thing to check: that each of the backend/compiler configurations are doing (roughly) the same amount of work.  Something that can happen when there is a tolerance set for termination is you can hit "unfortunate" parameters which cause the code to thrash through many needless iterations which don't actually get you any closer to the end result.  I have no idea if this is the case here, but because they don't behave quite like exactly rounded IEEE types, things like `double double` can easily break assumptions present in the code.

---

## Comment 48

> Username: ckormanyos  
> Created_at: 2025-01-19 17:20:47 UTC  
> Updated_at: 2025-01-19 17:21:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2600950005  

> There might be one more thing to check: that each of the backend/compiler configurations are doing (roughly) the same amount of work. Something that can happen when there is a tolerance set for termination is you can hit "unfortunate" parameters which cause the code to thrash through many needless iterations which don't actually get you any closer to the end result.  
  
Indeed. There are several potential dangers.  
  
Let's say we use `cpp_double_double` and a particular tolreance $dx$ is set to  
  
$$  
|dx| < 1^{-300}  
$$  
  
At the same time, we know that `min_exponent10` for the type is something like $-291$. So the tolerance is never reached or reached after useless iterations.  
  
Even worse, this backend is new, so there might be undiscovered problems in the areas of subnormal/zero. So you might iterate until the maximum iteration setting.  
  
We actually had several cases like this when John helped me _see_ through the last tricky spots in the _specfun_  tests. Who knows if we really got all the edge cases?  
  
Cc: @jzmaddock and @cosurgi

---

## Comment 49

> Username: ckormanyos  
> Created_at: 2025-04-19 13:44:28 UTC  
> Updated_at: 2025-04-19 13:44:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2816714761  

So I have updated this PR to the post-1.88 develop branch of Multiprecision. And it's going green once again (with the updated CI). There were a few trip-ups along the way, but nothing out of the ordinary.  
  
It's time to continue working the known final points in [BoostGSoC21/multiprecision/issues/160](https://github.com/BoostGSoC21/multiprecision/issues/160).  
  
I'm not sure if all this will be ready for 1.89, but there is a chance.  
  
Cc: @cosurgi and @jzmaddock and @sinandredemption

---

## Review 50 [Commented]

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:41:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/648#pullrequestreview-2937144007  

Just some mild observations while we're waiting for this to land.

📁 .gitignore

```diff
   9 |+ .idea/*
  10 |+ 
  11 |+ # CMake Related Options
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:29:05 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153203476  

Why is every line in this file changed?  Did you inadvertently change EOL chars?


📁 doc/reference_cpp_double_fp_backend.qbk

```diff
   1 |+ [/
   2 |+   Copyright 2021 - 2024 Fahad Syed.
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:29:38 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153204280  

2025 copyright?


📁 doc/tutorial.qbk

```diff
   1 | [/
   2 |-   Copyright 2011 - 2020 John Maddock.
   2 |+   Copyright 2011 - 2024 John Maddock.
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:29:51 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153204539  

2025?


📁 doc/tutorial_cpp_double_fp_backend.qbk

```diff
   1 |+ [/
   2 |+   Copyright 2021 - 2024 Fahad Syed.
   3 |+   Copyright 2024 Christopher Kormanyos.
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:30:02 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153204746  

2025?

---

📁 doc/tutorial_cpp_double_fp_backend.qbk

```diff
  25 |+ 
  26 |+ The `cpp_double_fp_backend` back-end is the sum of two IEEE floating-point numbers
  27 |+ combined to create a type having rougly twice the composite width of one of its parts.
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:30:28 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153205285  

`rougly` should be `roughly`

---

📁 doc/tutorial_cpp_double_fp_backend.qbk

```diff
  55 |+ and all zeros are treated as positive.
  56 |+ 
  57 |+ The `cpp_double_fp_backend` types inoteroperate with Boost.Math and Boost.Math.Constants.
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:32:09 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153207365  

`inoteroperate` should be `interoperate`


📁 doc/tutorial_floats.qbk

```diff
   1 | [/
   2 |-   Copyright 2011 - 2020 John Maddock.
   2 |+   Copyright 2011 - 2024 John Maddock.
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:33:44 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153209198  

2025?


📁 example/cpp_double_double_del_v_jv.cpp

```diff
   1 |+ ///////////////////////////////////////////////////////////////////////////////
   2 |+ //  Copyright Christopher Kormanyos 2023.
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:34:15 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153209840  

2025?


📁 example/cpp_double_fp_gamma_bessel.cpp

```diff
   1 |+ ///////////////////////////////////////////////////////////////
   2 |+ //  Copyright Christopher Kormanyos 2021 - 2023.
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:34:41 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153210292  

2025?


📁 include/boost/multiprecision/detail/functions/pow.hpp

```diff
 193 |    si_type n;
 195 |-    T       term, part;
 194 |+    T       term;
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:37:44 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153213941  

copyright year needs an update?


📁 performance/Jamfile.v2

```diff
  73 |             performance_test_files/test16.cpp  performance_test_files/test33.cpp  performance_test_files/test50.cpp
  74 |-             performance_test_files/test17.cpp  performance_test_files/test34.cpp  performance_test_files/test51.cpp            
  74 |+             performance_test_files/test17.cpp  performance_test_files/test34.cpp  performance_test_files/test51.cpp
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:38:24 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153214683  

copyright year needs an update?


📁 include/boost/multiprecision/float128.hpp

```diff
 264 |          buf2.reset(new char[v + 3]);
 265 |-          v = quadmath_snprintf(&buf2[0], v_max + 3, format.c_str(), digits, m_value);
 265 |+          v = quadmath_snprintf(&buf2[0], static_cast<std::size_t>(v_max + 3), format.c_str(), digits, m_value);
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:38:31 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153214797  

copyright year needs an update?


📁 performance/performance_test.cpp

```diff
  24 | // TEST_CPP_INT_RATIONAL
  25 | // TEST_CPP_BIN_FLOAT
  26 |+ // TEST_CPP_DOUBLE_FLOAT
```

> Username: LegalizeAdulthood  
> Created_at: 2025-06-17 21:38:39 UTC  
> Updated_at: 2025-06-17 21:41:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#discussion_r2153214974  

copyright year needs an update?  
  
Same for other files modified.


---

## Comment 51

> Username: ckormanyos  
> Created_at: 2025-06-18 20:48:15 UTC  
> Updated_at: 2025-06-18 20:49:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2985644235  

Hi @LegalizeAdulthood thank you for your review points. I will get to these. Your points seems sensible.  
  
It's been a while in the making, but I use this backend already locally and it seriously accelerates perturbative Mandelbrot calculations like by a factor of 3. No other backend that I am aware of **kicks it** like this one --- at double-double for about 32 digits.  
  
I do not know if/when I'll get this Boost-ready, but I'm still on it.  
  
Cc: @jzmaddock

---

## Comment 52

> Username: ckormanyos  
> Created_at: 2025-06-18 20:53:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2985656242  

Hi @LegalizeAdulthood I'll leave all the conversations open for now. I need to get back to these a bit later. Thanks again for contributing.

---

## Comment 53

> Username: LegalizeAdulthood  
> Created_at: 2025-06-18 22:12:22 UTC  
> Updated_at: 2025-06-18 22:13:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2985836917  

> I use this backend already locally and it seriously accelerates perturbative Mandelbrot calculations like by a factor of 3. No other backend that I am aware of kicks it like this one  
  
Nice!  My friend integrated the QD library into his [ManPWin](https://github.com/PaulTheLionHeart/manpwin) and I think he reported  a significant speedup as well.  
  
I think currently, at least for open source fractal renderers, kalles fraktaler 3 is one of the fastest out there, if not the fastest.  He has SIMD and GPU (OpenCL) paths, I haven't studied the code extensively enough to know the full details though.  
  
If I can be of assistance in helping this pull request be accepted, let me know.  It will ultimately help me too `:)`

---

## Comment 54

> Username: ckormanyos  
> Created_at: 2025-06-19 10:16:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2987538642  

> If I can be of assistance in helping this pull request be accepted, let me know.   
  
Hi Richard (@LegalizeAdulthood) if you get a chance, you could consider using the `cpp_double_fp_backend` backend. Boost.Multiprecision is header-only. So if you checkout the `cpp_double_fp_backend_integration` branch, you can immediately use classes such as `boost::multiprecision::cpp_double_double`.  
  
Other than that, I think there are still some edge cases in rounding and round-tripping. Those are the only open points left. I deactivated many of the tests in rounding and round-tripping and I think these _should_ pass. I'll need to talk with John about these sometime down the road.

---

## Comment 55

> Username: ckormanyos  
> Created_at: 2025-06-19 11:31:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2987736408  

> Just some mild observations while we're waiting for this to land.  
  
Handled in 748b7511aec5aefcae4a0ab5e61e28369362d480

---

## Comment 56

> Username: ckormanyos  
> Created_at: 2025-06-19 20:43:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/648#issuecomment-2989116633  

Redundant with #515

---
