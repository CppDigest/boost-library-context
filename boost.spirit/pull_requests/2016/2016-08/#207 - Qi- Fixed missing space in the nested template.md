# #207 Qi: Fixed missing space in the nested template [Merged]

> Username: Kojoley  
> Created at: 2016-08-18 09:47:06 UTC  
> Updated at: 2017-11-12 21:58:22 UTC  
> Merged at: 2016-08-24 22:54:41 UTC  
> Closed at: 2016-08-24 22:54:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/207  

The bug was introduced in PR #201

---

## Comment 1

> Username: Kojoley  
> Created_at: 2016-08-24 16:08:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/207#issuecomment-242121146  

@djowel can I ask you to merge this PR?

---

## Comment 2

> Username: djowel  
> Created_at: 2016-08-24 22:54:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/207#issuecomment-242233660  

Right! :-) Thanks, Kojoley.

---

## Comment 3

> Username: mxc-commons  
> Created_at: 2016-08-24 23:14:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/207#issuecomment-242237245  

I am aware that the extra space was required because of a potential ambiguity with `operator >>` parsers couldn't cope with.  
  
Are there still compilers requiring this? I'd like to add one of these to my test environment.

---

## Comment 4

> Username: djowel  
> Created_at: 2016-08-24 23:38:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/207#issuecomment-242241085  

It is required with all pre C++11 compilers (C++03)

---

## Comment 5

> Username: mxc-commons  
> Created_at: 2016-08-25 00:15:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/207#issuecomment-242246978  

msvc-9.0 is pre-11 is in my environment and has no problems. That's why I  
asked.  
  
maxence business consulting gmbh  
Frank Hein  
Geschäftsführer  
Am Weißen Stein 1  
41541 Dormagen  
T  +49 2133 - 2599-10  
F  +49 2133 - 2599-29  
M +49 151 - 54742989  
E frank.hein@maxence.de

---

## Comment 6

> Username: djowel  
> Created_at: 2016-08-25 00:31:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/207#issuecomment-242249285  

msvc-9.0 may be pre-c++11 but implements some C++11. Check if it has a flag with strict C++03 only. I doubt it. I don't know a strictly C++03 version of msvc. Maybe you are better off with GCC for testing C++03-isms.

---

## Comment 7

> Username: mxc-commons  
> Created_at: 2016-08-25 01:28:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/207#issuecomment-242256885  

msvc can not be set precisely to a particular c++ standard. Would it make sense to define a particular "minimum compliance platform"? Qi is some days old now. Would gcc-6.1.0 set to c++03 be ok as such a reference platform?

---

## Comment 8

> Username: Kojoley  
> Created_at: 2016-08-25 20:42:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/207#issuecomment-242533016  

GCC and Clang checks are made to explicitly fail on `>>` in templates if you are using `-std=c++03` or `-std=c++98`. This behavior allows you to use the latest compiler for development and do not be afraid of breaking something for compilers that not support latest C++ standard. Feel the difference from VS and undisaable M$ extensions.

---
