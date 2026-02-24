# #634 Use char32_t as unicode encoding char_type [Merged]

> Username: theodelrieu  
> Created at: 2020-12-31 17:43:44 UTC  
> Updated at: 2021-01-04 08:51:08 UTC  
> Merged at: 2021-01-03 00:21:08 UTC  
> Closed at: 2021-01-03 00:21:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/634  

Allows to use a `symbols_parser` with unicode encoding.

---

## Comment 1

> Username: theodelrieu  
> Created_at: 2020-12-31 18:17:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753020518  

@djowel I did let some duplicated code in the second commit, let me know where I should put this helper ;)  
  
Kinda fixes #576 (it only fixes the default handler).

---

## Comment 2

> Username: djowel  
> Created_at: 2021-01-01 02:38:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753243839  

Nice! I've been wanting to do that for a long time! Looks good to me. I'll wait for CI to finish (hopefully it will!).

---

## Comment 3

> Username: theodelrieu  
> Created_at: 2021-01-01 14:36:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753324317  

It seems that some C++03 jobs break, I couldn't reproduce on my Linux box with `cxxflags="-std=c++03"`. The logs do not mention anything related to the patch though, are those failure happening on `develop` already?

---

## Comment 4

> Username: djowel  
> Created_at: 2021-01-02 10:39:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753457997  

> It seems that some C++03 jobs break, I couldn't reproduce on my Linux box with `cxxflags="-std=c++03"`. The logs do not mention anything related to the patch though, are those failure happening on `develop` already?  
  
Does not seem to be related alright. Hmmm...

---

## Comment 5

> Username: djowel  
> Created_at: 2021-01-03 00:20:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753547953  

I'll merge this now.   
  
The CI failures look odd. It seems the same tests are passing with the same compiler in other instances. It seems the c++03 mode are the ones that are failing. Maybe it's time to get rid of c++03 mode to lessen the burden of maintenance. In any case, the issue with test is illerevant here.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2021-01-03 00:37:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753549359  

No, they are not odd, the cause is `char32_t` which does not exist in C++03.

---

## Comment 7

> Username: djowel  
> Created_at: 2021-01-03 00:56:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753550753  

> No, they are not odd, the cause is `char32_t` which does not exist in C++03.  
  
Ah darned! Of course! I should have inspected the error messages more closely. Can you fix this @theodelrieu ? I'm thinking about removing support for c++03, but that will not happen anytime soon.

---

## Comment 8

> Username: djowel  
> Created_at: 2021-01-03 00:57:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753550866  

> I'm thinking about removing support for c++03, but that will not happen anytime soon.  
  
And that will involve a lot of work as well. So our best option is to fix this issue for now.

---

## Comment 9

> Username: djowel  
> Created_at: 2021-01-03 01:21:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753553026  

@theodelrieu , I reverted the merge for now pending the fix for c++03: https://github.com/boostorg/spirit/pull/635

---

## Comment 10

> Username: theodelrieu  
> Created_at: 2021-01-04 08:51:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/634#issuecomment-753845405  

Oops, I'll fix it.

---
