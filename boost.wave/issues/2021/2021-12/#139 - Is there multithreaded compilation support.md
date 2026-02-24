# #139 - Is there multithreaded compilation support? [Closed]

> Username: w4454962  
> Created at: 2021-12-31 10:43:56 UTC  
> Updated at: 2022-01-01 11:53:11 UTC  
> Closed at: 2022-01-01 11:53:11 UTC  
> Url: https://github.com/boostorg/wave/issues/139  

For example, it takes more than 1 minute to preprocess and compile a 10MB source file, which is really too slow

---

## Comment 1

> Username: jefftrull  
> Created at: 2021-12-31 15:35:16 UTC  
> Url: https://github.com/boostorg/wave/issues/139#issuecomment-1003402653  

In my view performance has not been worked on very seriously in Wave - though @hkaiser will know better. "PRs welcome" as they say :)  
  
I am curious about your phrasing, though - when you say "to preprocess _and compile_" what do you mean? Wave only does preprocessing.

---

## Comment 2

> Username: jefftrull  
> Created at: 2021-12-31 15:42:45 UTC  
> Url: https://github.com/boostorg/wave/issues/139#issuecomment-1003404124  

You may be interested in the discussion on this old (pre-Github) ticket: https://svn.boost.org/trac10/ticket/1670

---

## Comment 3

> Username: w4454962  
> Created at: 2021-12-31 16:11:09 UTC  
> Url: https://github.com/boostorg/wave/issues/139#issuecomment-1003409625  

Sorry, my wording is a little unclear. It refers to the time when the preprocessing macro is expanded

---

## Comment 4

> Username: hkaiser  
> Created at: 2021-12-31 16:27:40 UTC  
> Url: https://github.com/boostorg/wave/issues/139#issuecomment-1003412969  

> You may be interested in the discussion on this old (pre-Github) ticket: https://svn.boost.org/trac10/ticket/1670  
  
Thanks @jefftrull for digging this up. Indeed, setting `BOOST_WAVE_SUPPORT_THREADING=0` should help a lot.

---

## Comment 5

> Username: w4454962  
> Created at: 2022-01-01 11:53:02 UTC  
> Url: https://github.com/boostorg/wave/issues/139#issuecomment-1003547105  

Thank you. The speed has been greatly improved after the modification.
