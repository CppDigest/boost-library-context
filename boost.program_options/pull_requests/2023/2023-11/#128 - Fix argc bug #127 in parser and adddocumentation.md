# #128 Fix argc bug #127 in parser and adddocumentation [Merged]

> Username: Superlokkus  
> Created at: 2023-11-30 21:56:14 UTC  
> Updated at: 2023-12-16 18:35:21 UTC  
> Merged at: 2023-12-16 18:35:09 UTC  
> Closed at: 2023-12-16 18:35:09 UTC  
> Url: https://github.com/boostorg/program_options/pull/128  

Hi Vladimir,  
  
as discussed in #127 . Also added documentation, with the intention to avoid regression or stupid questions like mine in the future :-) .

---

## Review 1 [Commented]

> Username: sehe  
> Created_at: 2023-11-30 22:56:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/program_options/pull/128#pullrequestreview-1758626943  

📁 include/boost/program_options/parsers.hpp

```diff
 123 |-             list. The parameters should be the same as passed to 'main'.
 123 |+             list. The parameters should be the same as passed to 'main', meaning:
 124 |+             @param argc Must be non-zero i.e. >= 0
```

> Username: sehe  
> Created_at: 2023-11-30 22:54:44 UTC  
> Updated_at: 2023-11-30 22:56:20 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1411383389  

Two things. Non-zero is >0. Secondly, I think the standard mandates a argc>=1 and argv[0] is the program name on all known platforms

> Username: Superlokkus  
> Created_at: 2023-11-30 23:43:04 UTC  
> Updated_at: 2023-12-01 00:15:46 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1411420254  

I just had a discussion about that prior with a theoretical physicist: according to him, everyone in the math world defines _edit_:  non-negative _end edit_  with >=0. Also got references: https://mathworld.wolfram.com/Nonnegative.html   
  
Math world besides, as mentioned in bug report, cpp standard seem to share this definition too. As it defines the behavior for argc == 0 cases.

> Username: Superlokkus  
> Created_at: 2023-11-30 23:55:42 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1411430476  

To the second point, see comment below

> Username: sehe  
> Created_at: 2023-11-30 23:58:16 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1411432983  

> Also got references: https://mathworld.wolfram.com/Nonnegative.html  
  
That's Nonnegative, though

> Username: Superlokkus  
> Created_at: 2023-12-01 00:07:36 UTC  
> Updated_at: 2023-12-01 00:07:37 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1411440113  

Ahh I see. Excuse my wrong comment, I pushed a correction just now

> Username: Superlokkus  
> Created_at: 2023-12-01 08:14:24 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1411753810  

Resolved?

> Username: sehe  
> Created_at: 2023-12-01 11:51:13 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1412013241  

Sure (I cannot find a button to mark as resolved)

> Username: Superlokkus  
> Created_at: 2023-12-01 11:56:59 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1412018209  

Well for some reason I can resolve it. But this of course only makes sense, if you would have :-)

---

📁 include/boost/program_options/parsers.hpp

```diff
 124 |+             @param argc Must be non-zero i.e. >= 0
 125 |+             @param argv argv[0] up to argv[argc-1] must point to null terminated strings and
 126 |+             argv[argc] must be 0 e.g. nullptr
```

> Username: sehe  
> Created_at: 2023-11-30 22:56:10 UTC  
> Updated_at: 2023-11-30 22:56:20 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1411384464  

why was this added here? It appears as though this restriction is never relied on here (in fact neither `argc` or `argv` are in sight). Perhaps this part of POSIX semantics should be documented elsewhere?

> Username: Superlokkus  
> Created_at: 2023-11-30 23:54:28 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1411429397  

I did not refer to posix but the [C++20 draft N 4860](https://isocpp.org/files/papers/N4860.pdf) and https://eel.is/c++draft/basic.start.main#2.2 in the bug report:   
To quote 6.9.3.1 main function [basic.start.main] (2.2) from the draft  
  
> If argc is nonzero these arguments shall be supplied in argv[0] through argv[argc-1] as pointers to the initial characters of null-terminated multibyte strings (ntmbss) (16.4.2.2.5.2) and argv[0] shall be the pointer to the initial character of a ntmbs that represents the name used to invoke the program or "". The value of argc shall be non-negative. The value of argv[argc] shall be 0.  
  
or in [the newer one](https://eel.is/c++draft/basic.start.main#2.2)  
  
So this comment shall make a regression of that bug less likely and state an API contract for function users and boost code contributors, what to expect.

> Username: sehe  
> Created_at: 2023-12-01 00:02:18 UTC  
> Updated_at: 2023-12-01 00:02:19 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#discussion_r1411436163  

Ah. My confusion was due to github UI putting the comment "together" with the preceding constructor overload. I was struggling to see how it related there :) Disregard


---

## Comment 2

> Username: ghost  
> Created_at: 2023-12-16 14:15:32 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#issuecomment-1858829026  

I found that this issue was mentioned as early as 2018.  
  
https://github.com/boostorg/program_options/pull/59

---

## Comment 3

> Username: vprus  
> Created_at: 2023-12-16 18:35:19 UTC  
> Url: https://github.com/boostorg/program_options/pull/128#issuecomment-1858902043  

Thanks, this PR was merged.

---
