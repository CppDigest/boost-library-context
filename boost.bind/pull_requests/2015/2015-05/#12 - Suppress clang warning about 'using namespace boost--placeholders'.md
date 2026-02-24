# #12 Suppress clang warning about 'using namespace boost::placeholders' [Merged]

> Username: jhunold  
> Created at: 2015-05-27 13:51:32 UTC  
> Updated at: 2015-05-27 19:53:06 UTC  
> Merged at: 2015-05-27 14:31:26 UTC  
> Closed at: 2015-05-27 14:31:26 UTC  
> Url: https://github.com/boostorg/bind/pull/12  

warning: using namespace directive in global context in header [-Wheader-hygiene]  
The warning is unhelpfull as this a backwards-compatibility fix.

---

## Comment 1

> Username: pdimov  
> Created_at: 2015-05-27 14:06:59 UTC  
> Url: https://github.com/boostorg/bind/pull/12#issuecomment-105924394  

Out of curiosity, is this a new warning or is it not on by default? Because I'm not seeing it.

---

## Comment 2

> Username: jhunold  
> Created_at: 2015-05-27 18:30:54 UTC  
> Url: https://github.com/boostorg/bind/pull/12#issuecomment-106025736  

Well, I use -Weverything and disable selected warnings.

---

## Comment 3

> Username: jhunold  
> Created_at: 2015-05-27 18:34:46 UTC  
> Url: https://github.com/boostorg/bind/pull/12#issuecomment-106026496  

The warning itself is several years old. The earliest discussion is from 2011, and I found a blog post from 2012 mentioning it. Unfortunately a full overview about all flags is missing which is the reason I use -Weverything and disable all warnings which make no sense for my project.

---

## Comment 4

> Username: pdimov  
> Created_at: 2015-05-27 19:53:06 UTC  
> Url: https://github.com/boostorg/bind/pull/12#issuecomment-106052781  

Well, at least you know about -Weverything. I tried -Wall -Wextra and didn't get -Wheader-hygiene. :-)

---
