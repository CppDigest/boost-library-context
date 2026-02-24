# #520 - Query tiny CI detail float128 specfun tests maybe not running [Closed]

> Username: ckormanyos  
> Created at: 2023-01-16 10:56:59 UTC  
> Updated at: 2023-01-16 13:23:22 UTC  
> Closed at: 2023-01-16 13:23:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/520  

Hi John (@jzmaddock) and Matt (@mborland),  
  
I believe I may have stumbled across an issue worth querying on the main issue list.  
  
While attempting to run `specfun` tests for a new backend, I found that both my intended `specfun` tests as well as those for `float128` seem like they might not actually be running in CI.  
  
Consider the following [build log](https://github.com/boostorg/multiprecision/actions/runs/3923348626/jobs/6706900478). Here we see that the `specfun` tests for `float128` go green, but actually a detailed look at the log reveals that no tests whatsoever seem to have been actually compiled and executed. In fact, we only `found 1 target` in the `test` phase of that CI run.  
  
I really am a beginner in integrating `specfun`, but I tend to thnk that these tests are actually not running.  
  
If this is the case, I feel maybe the requirement on the build checks `has_float128` (or it's called something like that) is `false` so no tests run in this particular detailed constellation.  
  
Ummmm.... What do you guys think? And if this suspicion is actually happenning, maybe I can get my new backend `specfun` running?  
  
Cc: @sinandredemption

---

## Comment 1

> Username: ckormanyos  
> Created at: 2023-01-16 11:19:42 UTC  
> Updated at: 2023-01-16 11:22:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/520#issuecomment-1383895325  

So just speculating...  
  
But in other projects, I've handled such false positives by doing crazy stuff like:  
  
- Pipe the build log to a temporary file.  
- `cat` the temporary file for build output.  
- `tail` the temporary file for build output.  
- And then `grep` or `gawk` the result of `tail`-ing to get a unanimous decision on test success.  
  
That's a long way to go. The other option, check it dilligently and get the build dependencies _absolutely_ right in the crazy VM/compiler combinations confronting the CI.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-01-16 11:36:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/520#issuecomment-1383915608  

This is intended: you're running the tests with c++20 rather than gnu++20  
and gnu extensions are required for __float128  
  
  
On Mon, 16 Jan 2023, 11:19 Christopher Kormanyos, ***@***.***>  
wrote:  
  
> So just speculating...  
>  
> But in other projects, I've handled such false positives by doing crazy  
> stuff like:  
>  
>    - Pipe the build log to a temporary file.  
>    - cat the temporary file for build output.  
>    - tail and then grep or gawk the temporyry build output file to get a  
>    unanimous decision on test success.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/multiprecision/issues/520#issuecomment-1383895325>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABGHSOF7ZP644B7OTVHJW5TWSUVFRANCNFSM6AAAAAAT4TDT64>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-01-16 11:57:09 UTC  
> Updated at: 2023-01-16 12:00:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/520#issuecomment-1383941975  

> This is intended: you're running the tests with c++20 rather than gnu++20 and gnu extensions are required for __float128  
  
Thanks John, I was afraid you'd say that. This brings up some follow-up questions.  
- Why run it at all on `-std=c++XX` if nothing is actually executed?  
- And important for my own next backend-step, ... Where is the right place to _find_ specfun tests running with `-std=gnu++XX`? So I can add my tests there.  
- Any more queries might be specific to my own  backend developments so i'll kind of stop there.  
  
Would it make sense to make a test-matrix group for `gnu++` standards on GHA. I know these exist on some of the ther CI services. But I couldn't find one on GHA.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2023-01-16 12:01:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/520#issuecomment-1383946780  

I guess it'd be possible to add, say, `gnu++14`, etc. to the test matrix for thos `specfun`s on GHA? Or is this just silly?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2023-01-16 13:02:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/520#issuecomment-1384023012  

The drone tests are all gnu++XX.  Just the way it got split.  
  
Thinking out loud here, we could `cat` the configuration build log to the end of the output if that would help in future?  It's in bin.v2/config.log BTW.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2023-01-16 13:23:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/520#issuecomment-1384057325  

> Thinking out loud here, we could `cat` the configuration build log to the end of the output if that would help in future? It's in bin.v2/config.log BTW.  
  
This is the kind of thing that _could_ help. Or it could turn out to be a stumbling block if it proves to be unreliable.  
  
If we can get these kinds of added checks to be reiliable, I usually do, in fact, do this kind of thing.  
  
Since I am in that area, I will take a look at feasibility/cost-benefit/reliability-prediction.  
  
I usually use a combination of `cat`, `tail`, and `grep` or `gawk` on `*nix`. I'll take a look.  
  
I'll also close this issue.  
  
Thanks John! Good advice as always!
