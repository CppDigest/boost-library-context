# #68 Provide a workaround for the missbehaving gcc compiler when using decltype inside a template function with an assignment. [Closed]

> Username: teajay-fr  
> Created at: 2014-11-06 23:32:31 UTC  
> Updated at: 2016-10-24 14:45:39 UTC  
> Closed at: 2014-11-27 21:33:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/68  

I have found a workaround for the gcc issue which prevent x3 from being usable with gcc.  
  
It seems the compiler handles the assignment operator specially and can't resolve the type inside the decltype() inside a template function.  
  
I didn't go through all the examples, I just updated the calc1.cpp example.  
Tell me what you think of this. If the solution I proposed is viable, I'll patch the rest of the samples.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-11-06 23:41:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/68#issuecomment-62072282  

Oh my... Different syntax. I hope there's a better solution. The precedence of <= is high up in the list (http://en.cppreference.com/w/cpp/language/operator_precedence), even higher than (Bitwise OR). Yes you can use another operator with lower precedence (e.g. one of the assignment operators), but honestly, I don't like it.  
  
IMO, let's not have workarounds like this. GCC should be fixed, not X3. I can consider implementation workarounds, but not interface workarounds.

---

## Comment 2

> Username: teajay-fr  
> Created_at: 2014-11-07 06:30:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/68#issuecomment-62104362  

I agree with you that it's not pretty, and that gcc should be fixed. But until it's done, x3 is only usable with clang. That was the intention of the workaround.

---

## Comment 3

> Username: djowel  
> Created_at: 2014-11-07 07:09:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/68#issuecomment-62106743  

let's try to find a workaround that does not involve changing the API

---

## Comment 4

> Username: djowel  
> Created_at: 2014-11-07 07:09:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/68#issuecomment-62106786  

in the meantime, how about if we push that to a branch, so people can start playing with x3?

---

## Comment 5

> Username: teajay-fr  
> Created_at: 2014-11-07 07:37:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/68#issuecomment-62108607  

Providing a gcc compatible branch would be a good idea, until we find a better solution or gcc gets fixed.

---

## Comment 6

> Username: teajay-fr  
> Created_at: 2014-11-07 16:33:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/68#issuecomment-62171266  

I've investigated further the gcc code and found a fix, for which I'm preparing a patch. But what I found out is that we have no chance of finding a work around with the current API. The problem comes from not correctly determining the type dependencies of the "=" operands inside a template definition. We can't get around that without changing something in the API.

---

## Comment 7

> Username: teajay-fr  
> Created_at: 2014-11-16 21:26:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/68#issuecomment-63240214  

Forget about the gcc patch. This is way to complicated to get it solved correctly. GCC defers the instanciation of the operator = in templates to the template instanciation site. I got the first error overcome, but gcc still fails to resolve the parse_rule overload correctly.  
I'm out of ideas of what I could do in the compiler, so I'll just leave it to the gcc devs.  
  
In the mean time I've come up with another proposition to work around the gcc problem. Please take a look at this.

---
