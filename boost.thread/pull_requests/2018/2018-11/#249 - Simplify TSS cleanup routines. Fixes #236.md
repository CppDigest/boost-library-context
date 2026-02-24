# #249 Simplify TSS cleanup routines. Fixes #236 [Merged]

> Username: Kojoley  
> Created at: 2018-11-17 12:30:27 UTC  
> Updated at: 2019-01-20 11:34:05 UTC  
> Merged at: 2019-01-03 18:10:53 UTC  
> Closed at: 2019-01-03 18:10:53 UTC  
> Url: https://github.com/boostorg/thread/pull/249  

Instead of wrapping a default or user provided destructor into a virtual  
class and placing it into a shared_ptr it is now stored directly with  
an elided type, to not introduce UB it is not called directly but through  
a helper function which casts it back to the original type before calling.  
  
I tried to touch as little lines as possible to make the review simpler.

---

## Comment 1

> Username: viboes  
> Created_at: 2018-11-18 08:01:17 UTC  
> Url: https://github.com/boostorg/thread/pull/249#issuecomment-439674747  

Thanks for working on this.  
I believed that the issue was the the sanitizer itself.  
Please, could you describe where the UB reported in #236 was so that I understand better the PR?  
  
@pdimov Please, could you check this and tell us what do you think?  
  
Of course, I couldn't accept this change for boost.1.69, as it is too late.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-11-18 11:22:50 UTC  
> Url: https://github.com/boostorg/thread/pull/249#issuecomment-439685321  

> I believed that the issue was the the sanitizer itself.  
  
There is no consensus. GGC and Clang devs says that it is not a bug in sanitizer.  
  
> Please, could you describe where the UB reported in #236 was so that I understand better the PR?  
  
The PR can be reviewed without thinking about #236, it is just a nice side effect.  
  
You can check my bug report to LLVM https://bugs.llvm.org/show_bug.cgi?id=39191 for the simple example.  
  
The problem is that virtual methods (`operator()` and destructor in the case) are called on objects (`delete_data`/`run_custom_cleanup_function`) that passes DSO boundaries as base (`tss_cleanup_function`), but their symbols are hidden. Making these symbols visible will solve the problem too, but as there is no consensus on the topic and because it is possible not to use virtual classes for solving the TSS needs.

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-11-18 14:01:30 UTC  
> Updated_at: 2018-11-18 14:02:22 UTC  
> Url: https://github.com/boostorg/thread/pull/249#issuecomment-439695069  

The original code is a bit odd, I suspect it dates from premodern times when shared_ptr didn't have deleters and boost::function didn't exist. Nowadays you could just use `shared_ptr<void>( value, func )`, or boost::function.  
  
The patch works too, I suppose. It's a matter of taste which one to pick.

---

## Comment 4

> Username: viboes  
> Created_at: 2018-11-19 18:27:52 UTC  
> Url: https://github.com/boostorg/thread/pull/249#issuecomment-439994081  

@Kojoley Could we summarize that there is no real bug and that the patch is just silencing the sanitizer? Are we on UB land?  
Could you clarify what is this fixing aside #236?  
  
@pdimov yes the code dates a lot.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2018-11-19 18:59:16 UTC  
> Url: https://github.com/boostorg/thread/pull/249#issuecomment-440004022  

> The original code is a bit odd, I suspect it dates from premodern times when shared_ptr didn't have deleters and boost::function didn't exist. Nowadays you could just use shared_ptr<void>( value, func ), or boost::function.  
  
The code is now *that* old, it is from 2007, and it was using boost::function  332dd988e461fda0b5d7c038aa3110c61979b8d6.  
  
> @Kojoley Could we summarize that there is no real bug and that the patch is just silencing the sanitizer?  
  
You can treat it like that.  
  
> Are we on UB land?  
  
This case is not documented to be UB, even throwing and catching hidden symbols is not explicitly banned (see boostorg/function#24).  
  
Again:  
  
> There is no consensus. GGC and Clang devs says that it is not a bug in sanitizer.  
  
> Could you clarify what is this fixing aside #236?  
  
Nothing.

---

## Comment 6

> Username: viboes  
> Created_at: 2018-11-21 20:59:28 UTC  
> Url: https://github.com/boostorg/thread/pull/249#issuecomment-440806599  

If this is not fixing nothing clear yet, why do you want I apply this PR then?

---

## Comment 7

> Username: Kojoley  
> Created_at: 2018-11-21 21:04:05 UTC  
> Url: https://github.com/boostorg/thread/pull/249#issuecomment-440807693  

OMG, you are killig me. The PR:  
  
  1. Removes (potential) UB.  
  2. Simplifies the code by reducing number of dependencies.  
  4. Makes TSS faster by removing `shared_ptr`.

---

## Comment 8

> Username: viboes  
> Created_at: 2019-01-03 18:10:47 UTC  
> Url: https://github.com/boostorg/thread/pull/249#issuecomment-451228474  

I've decided to give it a chance to this PR. We have now the time to fix it if there will be some regressions.

---
