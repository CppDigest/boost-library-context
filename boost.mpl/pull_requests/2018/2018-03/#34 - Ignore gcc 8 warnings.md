# #34 Ignore gcc 8 warnings. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2018-03-01 09:05:51 UTC  
> Updated at: 2018-03-07 22:25:06 UTC  
> Merged at: 2018-03-07 22:25:06 UTC  
> Closed at: 2018-03-07 22:25:06 UTC  
> Url: https://github.com/boostorg/mpl/pull/34  

Same as #33 but ignore warning instead of "fixing" it.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-03-03 22:30:11 UTC  
> Url: https://github.com/boostorg/mpl/pull/34#issuecomment-370185075  

I'm fine with this; thanks.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-03-05 00:12:17 UTC  
> Url: https://github.com/boostorg/mpl/pull/34#issuecomment-370277790  

Is gcc8 available on any Ljnux OSs ? I can not test this because even Fedora 27 has 7.3 as the latest gcc and mingw-64 has gcc 7.2 as the latest gcc..

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2018-03-05 00:48:07 UTC  
> Url: https://github.com/boostorg/mpl/pull/34#issuecomment-370280747  

I doubt you will find it in any stable distribution right now given that officially gcc 8 is not even released (will be at some point in April 2018). In my company we compile it ourselves.  
  
This page seems to hint that Fedora 28 (currently rawhide) has it already: https://apps.fedoraproject.org/packages/gcc

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2018-03-05 00:52:14 UTC  
> Url: https://github.com/boostorg/mpl/pull/34#issuecomment-370281149  

Note that in #31 some continuous build system is mentioned, so that may be for you an easier way you to validate this pull request actually silences the warning, rather than having to setup a unstable Fedora 28.

---

## Comment 5

> Username: Romain-Geissler-1A  
> Created_at: 2018-03-05 01:04:17 UTC  
> Url: https://github.com/boostorg/mpl/pull/34#issuecomment-370282296  

It is available in Debian testing as well (it's not the default "gcc" package, but you can install "gcc-8" instead): https://packages.debian.org/buster/gcc-8

---
