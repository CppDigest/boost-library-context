# #307 Fix all instances of MSVC warning C4146 (unsigned negation) [Merged]

> Username: TheJCAB  
> Created at: 2025-07-31 16:40:55 UTC  
> Updated at: 2025-08-12 17:12:50 UTC  
> Merged at: 2025-08-12 17:12:50 UTC  
> Closed at: 2025-08-12 17:12:50 UTC  
> Url: https://github.com/boostorg/container/pull/307  

Unsigned negation is a well-defined operation in C and C++ and it is commonly used when doing bit manipulation of unsigned integers, because it builds a high-pass bitmask.  
  
For instance, if `X` is known to be a power of two, `-X` is precisely the mask needed to align a value down to a multiple of `X` by means of a bit-wise `&` operation.  
  
However, it can occasionally be flagging a bug, when the original intent was mathematical negation. For this reason, an explicit fix is preferred to suppressing this warning.  
  
This change fixes all the instances of #303 by changing unsigned negation of the form `-X` to the more explicit `0 - X`, and removes the suppression.  
  
Ran all the tests under "test" and "bench". Fixed two more instances of the warning in one of the unit tests.  
  
I also included a drive-by spot-fix for the macro `left_bits(x)` whose parameter was missing the usual parenthesis. This was benign in all the uses of the macro.

---

## Comment 1

> Username: TheJCAB  
> Created_at: 2025-07-31 16:43:19 UTC  
> Url: https://github.com/boostorg/container/pull/307#issuecomment-3140645330  

@DougLea, FYI.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2025-07-31 22:41:56 UTC  
> Url: https://github.com/boostorg/container/pull/307#issuecomment-3141554562  

Since the dlmalloc implementation is not maintaned inside boost, we've avoided altering the original file, so that it can be more easily updated if new versions are released.

---

## Comment 3

> Username: TheJCAB  
> Created_at: 2025-07-31 23:10:02 UTC  
> Url: https://github.com/boostorg/container/pull/307#issuecomment-3141597991  

@igaztanaga I fully understand the concern. On the other hand, there have been no changes to the original in the past 12 years, and several changes of this sort (keeping up with issues raised by evolving toolsets and even fixing typos) in this copy of it.  
  
I have notified the original author above (and earlier in a short private email exchange). I'll be happy to adapt this change as needed based on his feedback, if any, or incorporate a new version if he wishes to publish it.

---

## Comment 4

> Username: GreciaNav  
> Created_at: 2025-08-07 16:41:43 UTC  
> Url: https://github.com/boostorg/container/pull/307#issuecomment-3164973337  

Hi all, my team would really benefit from this change, and I know other folks on the same boat. Adding a vote of this fix going in. Thank you!

---

## Comment 5

> Username: igaztanaga  
> Created_at: 2025-08-07 16:52:36 UTC  
> Url: https://github.com/boostorg/container/pull/307#issuecomment-3165012350  

I still don't understand why is this a problem. The warning is disabled in the file, it should not appear and should not affect any other code.

---

## Comment 6

> Username: TheJCAB  
> Created_at: 2025-08-07 19:00:23 UTC  
> Url: https://github.com/boostorg/container/pull/307#issuecomment-3165385698  

@igaztanaga "why is this a problem" -- There are short ways to answer and long ways to answer, and I'm not the most qualified to do either, but I'll try the short-ish.  
  
- Why now? Basically, Microsoft (where I work) is under a lot of pressure to identify and address any potential security issues impacting any part of "deemed critical" software, most notably Windows itself. You can read about it, for instance, here (section 4) (not light reading :-P): https://bidenwhitehouse.archives.gov/briefing-room/presidential-actions/2021/05/12/executive-order-on-improving-the-nations-cybersecurity/  
- Why this particular warning? In general, Microsoft tends to centralize initiatives (it's a way to keep things sane when you're so big) instead of determining things like compliance case-by-case, and in this case it's the SDL (Secure Development Initiative), under which this warning is considered "significant". The simplification here is... if you abide by this SDL, then you have that portion of the federal requirements covered. You can read more about it here: https://www.microsoft.com/en-us/securityengineering/sdl/?msockid=16243c919ec06e9c02c02fd79feb6f71  
- Why is disabling the warning not sufficient? The short answer here is... this warning has been deemed serious enough that SDL tooling here at Microsoft flags it even if it is disabled. We are required to address it, and the two main solutions is to locally apply a patch in all the places where boost(-container) is used, or to contribute it to the community here. I hope you agree that contributing is the preferred choice wherever possible, so here we are.  
- Why me? I care about this deeply, so I volunteered to do this change. My day job is working on Time Travel Debugging (https://aka.ms/ttd), where we have gone to extremes to keep the code clean, to the extent that we suppress or disable very few warnings (building with either MSVC or clang) and are also static analysis clean to the point that we tripped some tools that didn't like empty warning logs.  
  
I'd add that it's good code hygiene. Disabling a warning hides potential issues, both current and future, indiscriminately. A better way, arguably, if you wish to hide a particular instance of a warning, is with a suppression (`#pragma warning(suppress: 4146)` in MSVC) and include a justification.  
  
But suppressions are verbose, and a better way (IMHO) is just to change the code to be more explicit about its intent so the warning is no longer issued. In this case, the code is using negation on unsigned quantities (mostly `size_t`). While well defined by the language, this can be sometimes a true bug (and I'm sure the severity is the result of finding true bugs that would have been flagged by this). Explicitly subtracting from zero is a way to express the intent, as is explicitly casting (say, for other sorts of warnings).  
  
In case there's concern about code generation, all three main compilers generate the same code either way: https://godbolt.org/z/f994nKW3e  
  
Ok, that wasn't really that short-ish :-P.  
  
I hope you'll consider both the merit of this change and the needs of (some of) your users (multiple users within Microsoft that got this issue flagged).  
  
Thank you.

---

## Comment 7

> Username: DougLea  
> Created_at: 2025-08-07 19:14:36 UTC  
> Url: https://github.com/boostorg/container/pull/307#issuecomment-3165420875  

As original author... This change has no impact on compiled code; it just works around a limitation in MS tool chain. So I don't object to seeing it done in the boost version of dlmalloc. But I'm not very tempted to update the original.

---

## Comment 8

> Username: igaztanaga  
> Created_at: 2025-08-12 17:12:27 UTC  
> Url: https://github.com/boostorg/container/pull/307#issuecomment-3180262923  

Thanks @DougLea for your comment, thanks @TheJCAB for the pull request. I'll merge it.

---
