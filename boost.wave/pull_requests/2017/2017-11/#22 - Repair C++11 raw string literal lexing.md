# #22 Repair C++11 raw string literal lexing [Merged]

> Username: jefftrull  
> Created at: 2017-11-28 03:37:37 UTC  
> Updated at: 2017-11-28 21:05:37 UTC  
> Merged at: 2017-11-28 20:35:49 UTC  
> Closed at: 2017-11-28 20:35:49 UTC  
> Url: https://github.com/boostorg/wave/pull/22  

Presently Wave expects C++11 raw string literals to be of the form `R"some text"` but this is not correct.  The format is actually `R"delim(some text)delim"`, where `delim` can be empty and `some text` may include anything other than `)delim`.  
  
This PR adds code to the RE2C lexer to support the correct format, including checking delimiters for validity (permitted characters, length) and using them to quote arbitrary text which may include double quotes.  The raw string literal test is updated accordingly.  
  
If merged, this PR will resolve [TRAC 12603](https://svn.boost.org/trac10/ticket/12603).

---

## Comment 1

> Username: jefftrull  
> Created_at: 2017-11-28 03:46:15 UTC  
> Url: https://github.com/boostorg/wave/pull/22#issuecomment-347405063  

I just realized it will also resolve [TRAC 12602](https://svn.boost.org/trac10/ticket/12602)

---

## Review 2 [Changes requested]

> Username: hkaiser  
> Created_at: 2017-11-28 13:07:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/wave/pull/22#pullrequestreview-79494410  

Thanks a lot, Jeff!

📁 test/testwave/testfiles/t_7_001.cpp

```diff
  39 |- no_newline_at_end_of_file
  49 |\ No newline at end of file
  50 |+ no_newline_at_end_of_file
```

> Username: hkaiser  
> Created_at: 2017-11-28 13:00:24 UTC  
> Updated_at: 2017-11-28 13:40:05 UTC  
> Url: https://github.com/boostorg/wave/pull/22#discussion_r153483860  

This file is supposed to end without a trailing EOL, this accidentally adds it back in.


---

## Comment 3

> Username: jefftrull  
> Created_at: 2017-11-28 13:41:05 UTC  
> Url: https://github.com/boostorg/wave/pull/22#issuecomment-347526466  

Thanks for the heads up. My editor was apparently being "helpful".

---

## Review 4 [Approved]

> Username: hkaiser  
> Created_at: 2017-11-28 20:35:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/22#pullrequestreview-79647658  

LGTM, thanks!

---
