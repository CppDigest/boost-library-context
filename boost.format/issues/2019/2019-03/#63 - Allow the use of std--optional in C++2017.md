# #63 - Allow the use of std::optional in C++2017 [Open]

> Username: SeanFarrow  
> Created at: 2019-03-29 13:13:56 UTC  
> Updated at: 2020-09-08 18:35:29 UTC  
> Url: https://github.com/boostorg/format/issues/63  

It would be good if std::optional<T> were used in projects that use C++2017.  
  
A define like that which is available in boost.dll 1.70 would suffice in my opinion.

---

## Comment 1

> Username: jeking3  
> Created at: 2019-04-19 10:55:49 UTC  
> Url: https://github.com/boostorg/format/issues/63#issuecomment-484851233  

Could you be more specific, perhaps provide an example use?

---

## Comment 2

> Username: SeanFarrow  
> Created at: 2019-04-19 15:51:16 UTC  
> Url: https://github.com/boostorg/format/issues/63#issuecomment-484938557  

This isn’t something that is user facing.  
  
Within the boost format library boost::optional is used, what I’m asking for is a define to use std::optional instead, like boost.dll does with boost/std::filesystem.  
If I get a chance next week, I’ll create a PR.  
  
  
From: James E. King III <notifications@github.com>  
Sent: 19 April 2019 11:56  
To: boostorg/format <format@noreply.github.com>  
Cc: Sean Farrow <sean.farrow@seanfarrow.co.uk>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/format] Allow the use of std::optional in C++2017 (#63)  
  
  
Could you be more specific, perhaps provide an example use?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/format/issues/63#issuecomment-484851233>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AALDK7XI7HTCAB3JGGRASRLPRGQLNANCNFSM4HCJOEGQ>.

---

## Comment 3

> Username: AraHaan  
> Created at: 2020-09-08 06:24:30 UTC  
> Updated at: 2020-09-08 18:35:29 UTC  
> Url: https://github.com/boostorg/format/issues/63#issuecomment-688645357  

Isn't there an language macro that is defined the the compiler supports the standard optional instead of the boost one that can be checked against like there is with ``CXX_LIB_FILESYSTEM/CXX_LIB_EXPERIMENTAL_FILESYSTEM`` or something along those lines that can be used with boost?  
  
(unrelated but I want to also figure out things I can use in place of ``format`` and ``wformat`` used in this lib so that way a program of mine could be made a bit more optimized.)  
  
(hmm so is  
```cpp  
typedef lagged_fibonacci_01_engine<double, 48, 607, 273> lagged_fibonacci607;  
```  
the same as this?  
```cpp  
std::subtract_with_carry_engine<double, 48, 607, 273>  
```  
)
