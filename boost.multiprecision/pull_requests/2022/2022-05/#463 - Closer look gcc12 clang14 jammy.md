# #463 Closer look gcc12 clang14 jammy [Merged]

> Username: ckormanyos  
> Created at: 2022-05-19 05:04:25 UTC  
> Updated at: 2022-06-02 09:06:27 UTC  
> Merged at: 2022-05-23 07:00:16 UTC  
> Closed at: 2022-05-23 07:00:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-05-20 04:46:16 UTC  
> Updated_at: 2022-05-20 04:46:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463#issuecomment-1132457964  

Hi John (@jzmaddock) the initial tests on GCC12/clang14 in Jammy were mostly good. There is, however, one fail on clang14 using C++2b. It actually appears to be a legitimate error and not a timeout.  
  
I have not yet taken a closer look, but it seems like there might be a legit issue needing attention (if, that is, we'd like to compile with experimental `-std=c++2b`).

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-05-20 08:50:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463#issuecomment-1132647966  

Looks like an issue to me, will probably need a bit of "comparative debugging" to see why gcc and clang are giving different answers.  I don't have these compilers installed locally yet... still on the todo list!

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-05-20 09:41:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463#issuecomment-1132695965  

>  I don't have these compilers installed locally yet... still on the todo list!  
  
Yeah, me too. I've been _getting_ _lucky_ on the server farms.  
  
So... how would you like to proceed? It seems like the defect(s) are/were only found on one compiler with `-std=c++2b`.  
- I/we could eliminate that language standard from the test matrix and integrate the CI run, and add that standard back to the CI run(s) later.  
- Or we could wait a while and see if the problem can be isolated, then add the entire CI run(s)?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-05-20 18:25:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463#issuecomment-1133191806  

I would suspect a compiler or std lib issue, but you never know.  
  
I'm downloading Ubuntu-22 now, so give me a few days and I *may* have an answer...

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2022-05-21 11:57:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463#issuecomment-1133607343  

> suspect a compiler or std lib issue, but you never know.  
  
That makes sense. Indeed, I'm also sort of _feeling_ a compiler issue here.  
  
> I'm downloading Ubuntu-22 now, so give me a few days and I _may_ have an answer...  
  
Thanks John... Seems like it might be a tough one to narrow down.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2022-05-22 09:14:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463#issuecomment-1133852960  

One fix pushed, there is still a further issue that `std::is_constant_evaluated()` is returning true when it should not.  I need to investigate that further...

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2022-05-22 10:38:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463#issuecomment-1133866757  

Just peered at this... Maybe a `break` instead of the comment ` // fallthrough`, but I'm not entirely sure on the intent...

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2022-05-22 12:13:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463#issuecomment-1133882713  

Grrr, uploading legal code would have helped!  Hopefully corrected now.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2022-05-22 17:41:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/463#issuecomment-1133941311  

The clang issue is reported here: https://github.com/llvm/llvm-project/issues/55638  
  
We need to work around this as well as otherwise we call sub-optimal routines as it assumes everything is constexpr all the time!

---
