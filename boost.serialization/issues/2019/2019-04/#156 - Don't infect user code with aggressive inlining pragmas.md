# #156 - Don't infect user code with aggressive inlining pragmas [Closed]

> Username: lasersson  
> Created at: 2019-04-30 17:51:37 UTC  
> Updated at: 2020-07-18 17:57:33 UTC  
> Closed at: 2020-07-18 17:57:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/156  

Some headers (listed below) in this library has the following very aggressive inlining pragmas specified:  
```  
#if defined(_MSC_VER)  
# pragma once  
#pragma inline_depth(511)  
#pragma inline_recursion(on)  
#endif  
```  
First of all, 511 is not a valid value for `inline_depth` according to [the documentation](https://docs.microsoft.com/en-us/cpp/preprocessor/inline-depth?view=vs-2017) of recent MSVC versions.  
  
Secondly, and more importantly, these pragmas will leak into anything that directly or indirectly include these headers and affect the compilation of other code. This applies to _any_ code, even code that does not interface with `boost::serialization` in any way. This is a very unexpected side-effect from using this library.  
  
For example, I've seen compile times of _a single function_ go from milliseconds to over 30 seconds just because I include one of the headers.  
  
The affected files I managed to find by a simple search:  
- include/boost/archive/detail/check.hpp  
- include/boost/archive/detail/iserializer.hpp  
- include/boost/archive/detail/oserializer.hpp

---

## Comment 1

> Username: robertramey  
> Created at: 2019-04-30 18:05:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/156#issuecomment-488054884  

Hmmm - interesting observation.  
  
I'm sure that code was inserted to address an issue at some time.  Though of course I wouldn't remember now.  So have you verified that all tests pass when this code is surpressed?  I also note that you use the word "recent".  I'm reluctant to make a change on breaking something and getting into a whack-a-mole situation.  
  
How about we make a compromise - replace   
#if defined(_MSC_VER) with something like  
#if defined(_MSC_VER) && _MSC_VER < 42 or whatever  
  
This would leave in the original intent - presumable for some previous compiler, and provide the benefits that implementation of your suggestion would bring.

---

## Comment 2

> Username: lasersson  
> Created at: 2019-04-30 21:43:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/156#issuecomment-488125949  

Thanks for your reply! Yes, the serialization tests still pass with those removed. The only reason I used "recent" was because the linked page only allowed me to look at docs for the 2015, 2017 and 2019 versions 😃. I did a quick scan of [docs for older versions](https://docs.microsoft.com/en-us/previous-versions/visualstudio/) it seems values >255 have never been valid unless I'm missing something.  
  
After some more testing I found out the bigger problem (at least in my case) is the `inline_recursion` pragma. The way I read the documentation for `inline_recursion` is that it is supposed to be specified at the call site of an inline recursive function:  
  
> This pragma takes effect at the first function call after the pragma is seen and does not affect the definition of the function.  
  
This wording does not seem to have changed since at least VS 2008. So, IMHO it seems the pragma is not supposed to be used this way (who knows what the first function call after the pragma is seen is going to be in this case?) and it should probably be switched back off after it has been used

---

## Comment 3

> Username: robertramey  
> Created at: 2019-04-30 21:57:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/156#issuecomment-488129855  

LOL - welcome to the real world!  Even a seemingly minor change at this point can have a very inconvenient ripple effect so I'm always super careful.  
  
Looking back at git history, I see this has been in there since 2009.  Though I see it was altered in 2016 to make it more universal.  So at least someone complained that it was needed.  I don't recollect anyone investigating it to the depth that you have so I'll go with your suggestion.  
  
So ... fork the repo, make your changes, and submit a Pull Request.  I'll merge it and we'll watch the test matrix to see if anything blows up.  
  
I don't know if there is a way to switch the setting back to the original one - whatever that was.  You might take a look at that while you're in there.  
  
Thanks for spending your time looking into this.  It is due the efforts of many people like yourself that the library continues function (indeed, keep improving) after 15 years.  We all appreciated it.  
Robert Ramey

---

## Comment 4

> Username: robertramey  
> Created at: 2019-04-30 21:58:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/156#issuecomment-488130153  

PS see Commit:  
8af0e20bb091631a153147587d7958492be623ae [8af0e20]

---

## Comment 5

> Username: lasersson  
> Created at: 2019-04-30 23:25:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/156#issuecomment-488150564  

https://github.com/boostorg/serialization/pull/157

---

## Comment 6

> Username: robertramey  
> Created at: 2020-07-18 17:57:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/156#issuecomment-660518389  

for some reason I can't recall I looked at this in a little more depth.  It occurs to me that really we should always be using pragmas in such a way so as not to leak the pragma into other code.  Among other possible problems it introduces the possibility of header races.  Looking at  [MSVC Documentation ](https://docs.microsoft.com/en-us/cpp/preprocessor/pop-macro?view=vs-2019)  I'm wondering if we should surround all these cases with:  
``
