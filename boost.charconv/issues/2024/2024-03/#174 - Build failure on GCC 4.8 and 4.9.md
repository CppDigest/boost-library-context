# #174 - Build failure on GCC 4.8 and 4.9 [Closed]

> Username: grisumbras  
> Created at: 2024-03-25 15:55:59 UTC  
> Updated at: 2024-03-26 10:04:02 UTC  
> Closed at: 2024-03-26 07:11:16 UTC  
> Url: https://github.com/boostorg/charconv/issues/174  

https://drone.cpp.al/boostorg/json/1482/14/2  
  
I've also noticed that GCC 4.8 is not tested in CI. Is it not supported?

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-03-25 16:08:48 UTC  
> Updated at: 2024-03-25 19:13:53 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2018368228  

> * GCC 5 or later  
> * Clang 3.8 or later  
> * Visual Studio 2015 (14.0) or later  
  
Indeed it is not. The interesting thing here is that all of these compilers support C++14, so there's no need for Charconv to support C++11.

---

## Comment 2

> Username: mborland  
> Created at: 2024-03-26 07:11:16 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2019538478  

It is not. Recently there was an attempt to make them work that I was not interested in supporting: https://github.com/boostorg/charconv/pull/165 and https://github.com/boostorg/charconv/pull/170.   
  
> The interesting thing here is that all of these compilers support C++14, so there's no need for Charconv to support C++11.  
  
The support for C++14 is incomplete in all of those. To get everything working as it should in a different library the bar had to be raised a fair amount: https://cppalliance.org/decimal/overview.html

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-03-26 09:22:24 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2019914131  

I see. Unfortunately this blocks JSON from using Charconv for the time being.

---

## Comment 4

> Username: mborland  
> Created at: 2024-03-26 09:33:48 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2019936295  

> I see. Unfortunately this blocks JSON from using Charconv for the time being.  
  
Do you actually have demand for 4.X? Your benchmarks have huge gains by making the change. Add a note into 1.85 that says you plan to drop support in 1.86.

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-03-26 09:43:29 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2019959130  

> Do you actually have demand for 4.X? Your benchmarks have huge gains by making the change. Add a note into 1.85 that says you plan to drop support in 1.86.  
  
GCC 4.8 and 4.9 are used as default compilers on some older versions of Linux. I don't have a way to actually check how many people rely on support for these compilers.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-03-26 09:50:41 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2019972707  

Maybe you can ask on the mailing list and/or Reddit? The ML has a lot of reach because there are many subscribers who don't post.

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-03-26 09:53:31 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2019978711  

Great idea.

---

## Comment 8

> Username: mborland  
> Created at: 2024-03-26 09:55:55 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2019983644  

> > Do you actually have demand for 4.X? Your benchmarks have huge gains by making the change. Add a note into 1.85 that says you plan to drop support in 1.86.  
>   
> GCC 4.8 and 4.9 are used as default compilers on some older versions of Linux. I don't have a way to actually check how many people rely on support for these compilers.  
  
RHEL 7 goes to extended maintenance in May which seems to be the only major distro with <=4.8 as the default that is not EOL. Concur with asking the ML.

---

## Comment 9

> Username: pdimov  
> Created at: 2024-03-26 09:58:42 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2019991977  

RH killed CentOS so the only live one remains on 4.8, or did last I checked. There were some alternatives in development, but I don't follow them so I don't know what's the current state of things in CentOSland.

---

## Comment 10

> Username: pdimov  
> Created at: 2024-03-26 10:01:25 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2020001712  

> CentOS-7 updates until June 30, 2024  
  
Goes EOL soon. :-)

---

## Comment 11

> Username: mborland  
> Created at: 2024-03-26 10:04:00 UTC  
> Url: https://github.com/boostorg/charconv/issues/174#issuecomment-2020006796  

> RH killed CentOS so the only live one remains on 4.8, or did last I checked. There were some alternatives in development, but I don't follow them so I don't know what's the current state of things in CentOSland.  
  
Alma and Rocky are the two big ones I know of, and they are both RHEL 8 derivatives which has GCC 8 as default.   
  
> CentOS-7 updates until June 30, 2024  
  
Perfect
