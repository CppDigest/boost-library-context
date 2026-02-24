# #60 Fixes a potentional crash with certain regexes [Closed]

> Username: aronpgit  
> Created at: 2018-03-28 14:45:19 UTC  
> Updated at: 2018-07-24 18:45:45 UTC  
> Closed at: 2018-07-24 18:45:45 UTC  
> Url: https://github.com/boostorg/regex/pull/60  

Some regular expressions with nested tags can cause a crash in boost::match_results::format(...). With this fix it will throw an exception instead of dying.

---

## Comment 1

> Username: aronpgit  
> Created_at: 2018-03-28 14:51:43 UTC  
> Url: https://github.com/boostorg/regex/pull/60#issuecomment-376915408  

Attaching the find expression, replace with expression and the text to search and replace in to see the crash:  
  
[find_expression.txt](https://github.com/boostorg/regex/files/1856465/find_expression.txt)  
[replace_expression.txt](https://github.com/boostorg/regex/files/1856466/replace_expression.txt)  
[text_to_search.txt](https://github.com/boostorg/regex/files/1856467/text_to_search.txt)

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-07-24 18:45:45 UTC  
> Url: https://github.com/boostorg/regex/pull/60#issuecomment-407511880  

I'm unable to reproduce this - I did fix a bunch of issues found by fuzz-testing back a while, so I'm hoping this is one of those.

---
