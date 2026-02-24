# #241 fix(lexer): support size_t literal, separator chars, 'module' [Closed]

> Username: APokorny  
> Created at: 2025-08-23 21:56:05 UTC  
> Updated at: 2025-10-27 16:27:53 UTC  
> Closed at: 2025-10-27 16:27:09 UTC  
> Url: https://github.com/boostorg/wave/pull/241  

This extends the lexers with some of the things that seemed to be missing.  
For the separator chars in number literals ', the spirit parsers would need to be adjusted.  
I do not remember, whether that could be done by wrapping a scanner or by replacing the parsers..  
Additionally I was not sure, at what point of wave these spirit grammars play a role.  
After that I am curious about user defined literals...

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2025-08-24 14:45:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/241#pullrequestreview-3149371842  

LGTM, thanks!

---

## Comment 2

> Username: APokorny  
> Created_at: 2025-09-05 15:54:51 UTC  
> Url: https://github.com/boostorg/wave/pull/241#issuecomment-3258865078  

A few days ago I added  user defined and standard library literal tokens. re2c works fine, but I ran into issues with xpressive. I hope I can get back to it soon.  Should changes for that go into the same .. or a different branch?

---

## Comment 3

> Username: jefftrull  
> Created_at: 2025-09-27 03:07:35 UTC  
> Url: https://github.com/boostorg/wave/pull/241#issuecomment-3341094030  

I withdraw my previous comment :) I think we should put all related functional changes into the same PR and handle all the various lexers in parallel.  
  
I do have a mild preference for separating _features_ (keywords, number separators, literals), into separate commits/PRs, but if they are logically related it's fine.  
  
Any thoughts on the CI failures?

---

## Comment 4

> Username: jefftrull  
> Created_at: 2025-09-27 03:36:27 UTC  
> Url: https://github.com/boostorg/wave/pull/241#issuecomment-3341113615  

I see you have also upgraded re2c from 1.0.2 to 4.1. Actually I'm very interested in trying newer versions of re2c, but I think we should make that a separate PR (and also record what we expect to get in terms of improvements etc.). Can you build with 1.0.2 for now?

---

## Review 5 [Commented]

> Username: jefftrull  
> Created_at: 2025-09-27 04:19:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/241#pullrequestreview-3274505216  

📁 samples/cpp_tokens/slex/cpp_slex_lexer.hpp

```diff
 137 |- #define OPTSIGN             "[-+]?"
 137 |+ #define BINARYDIGIT         "[01]"
 138 |+ #define SIGN             "[-+]?"
```

> Username: jefftrull  
> Created_at: 2025-09-27 04:19:41 UTC  
> Updated_at: 2025-09-27 04:19:42 UTC  
> Url: https://github.com/boostorg/wave/pull/241#discussion_r2383842109  

Minor, but can we leave this as `OPTSIGN`? More exact, and also this `SIGN` doesn't line up with the other strings


---

## Comment 6

> Username: jefftrull  
> Created_at: 2025-09-27 23:35:30 UTC  
> Url: https://github.com/boostorg/wave/pull/241#issuecomment-3342114528  

We should also think a bit about language version support here also. Digit separators were introduced in C++14, which we don't have a separate flag for, but you could argue "0x" would be appropriate at least. size_t literals are C++23 and we don't have a flag for that yet.

---

## Comment 7

> Username: jefftrull  
> Created_at: 2025-10-27 00:06:36 UTC  
> Url: https://github.com/boostorg/wave/pull/241#issuecomment-3449037914  

@APokorny I have combined this PR with some bug fixes and the feedback I mentioned above; if you're comfortable with it I will merge #242

---

## Comment 8

> Username: APokorny  
> Created_at: 2025-10-27 07:09:21 UTC  
> Url: https://github.com/boostorg/wave/pull/241#issuecomment-3449812165  

Thats great! - I was pulled off into other projects and had no more time working on this in the last few weeks.

---

## Comment 9

> Username: jefftrull  
> Created_at: 2025-10-27 16:27:09 UTC  
> Updated_at: 2025-10-27 16:27:53 UTC  
> Url: https://github.com/boostorg/wave/pull/241#issuecomment-3452220391  

Closed in favor of the related #242 which is a superset

---
