# #334 - Possibility of enabling Hana for VS2017 with the v141 toolset (_MSC_VER=1910)? [Closed]

> Username: fuchstraumer  
> Created at: 2017-04-08 20:59:09 UTC  
> Updated at: 2017-04-08 21:18:35 UTC  
> Closed at: 2017-04-08 21:18:35 UTC  
> Url: https://github.com/boostorg/hana/issues/334  

I'd love to use Hana, but I'm not quite able to move entirely away from MSVC, unfortunately. A bit of kludgineering and smacking hana/config.hpp with a wrench to allow Hana to  be included/enabled with MSVC results in a pile of errors, unsurprisingly. Checking for _MSC_VER >= 1910 and then defining "BOOST_HANA_CONFIG_LIBMSVCCXX" (no idea what I'm doing, only seemed vaguely relevant) leads to only one error though, so either the parser is giving up or things might just work with MSVC's latest version.  
  
The error is in "hana/fwd/optional.hpp", line 343: `defaulted default constructor cannot be constexpr because the corresponding implicitly declared default constructor would not be constexpr`  
  
code in question:  
`constexpr optional() = default;`  
  
As far as I can tell TMP and most of what Hana does is black magic (but I'm trying to muddle my way through it), so I apologize if I'm not of much aid. This is just the furthest I've been able to get on compiling Hana and I noticed you commented on [this article](https://blogs.msdn.microsoft.com/vcblog/2016/06/07/expression-sfinae-improvements-in-vs-2015-update-3/) quite some time ago, and I managed to find that article while looking for workarounds to get Hana compiling.  
  
Would just be nice to know, if its not too much of a bother, if support is either possible now or could be possible at some point in the future. Cheers!  
  
quick edit: commenting out / changing the line causing an error just leads to a different error, so looks like its the parser getting upset.

---

## Comment 1

> Username: ldionne  
> Created at: 2017-04-08 21:03:42 UTC  
> Url: https://github.com/boostorg/hana/issues/334#issuecomment-292745512  

The problem is that only `#include`ing Hana headers won't show you much. If you try to use anything, I'd expect to see many more errors that will most likely not be fixable. I know they're working on getting MSVC to compile Hana (in their internal test suite, I believe), but IDK how far they are into it. I don't think that's something I can do at the library level.

---

## Comment 2

> Username: ldionne  
> Created at: 2017-04-08 21:04:29 UTC  
> Url: https://github.com/boostorg/hana/issues/334#issuecomment-292745562  

Also, the fact that I don't have access to VS locally does not help at all.

---

## Comment 3

> Username: fuchstraumer  
> Created at: 2017-04-08 21:10:15 UTC  
> Url: https://github.com/boostorg/hana/issues/334#issuecomment-292745851  

No worries! Thanks for replying though. I have a family member and a close friend who work on the Visual Studio team - I'll ask around and see if they've made any progress. If they have, I would hope they'd submit a PR or something to help others out. Extensive googling hasn't turned up any real leads.   
  
And you were right entirely - compiling Hana made a whole bunch of compiler errors come up, haha. Oh well - Hana wasn't vital for what I wanted to do, but it would've made things so much easier

---

## Comment 4

> Username: ldionne  
> Created at: 2017-04-08 21:18:35 UTC  
> Url: https://github.com/boostorg/hana/issues/334#issuecomment-292746325  

I'm sorry. As soon as there's a reasonable path towards MSVC support, I'll be really open to try and make it work. I know they're getting there, but for now it would be close to impossible. Good luck, and do post here if you have news from your contacts, I'd like to get an update too!
