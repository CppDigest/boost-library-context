# #87 - Levels of support for various versions of the C++ standard? [Closed]

> Username: wizard69  
> Created at: 2020-05-08 19:42:51 UTC  
> Updated at: 2020-08-12 15:32:23 UTC  
> Closed at: 2020-08-12 15:32:07 UTC  
> Url: https://github.com/boostorg/json/issues/87  

For brand new libraries do is really make sense to try to support compilers all the way back to C++11.   By the time this becomes an official boost project it will have been well past 10 years.  
  
also even though I think I know what you mean, the phrase: "The library is statically or dynamically linked by default" grates my sense of English.  One or the other should be a defualt and the other an option.   The smae applies for the line aobut build scripts.  
  
The sixth item kinda highlights my first comment.   It looks like you could easily be C++ standard only by simply coding to a more current standard.   Going all the way back to C++11 is likely adding excessive complexity to your code.    More so you likely will not want to support that compatibility code for too long.   If you need C++17 to write the library then drop C++11 support.   By the way I'm looking at this form the standpoint of the library taking a year or two to even be incorporated into Boost and then evne longer to get folded intothe C++ standard.  
  
By the way I do hope that the drive here is inclusion in the C++ standard.   C++ is a bit behind with respect to libraries for standardised files.  I hope commenting in this manner doesn't upset anybody but Reddit isn't ideal for me

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-09 00:57:13 UTC  
> Url: https://github.com/boostorg/json/issues/87#issuecomment-626078994  

So Boost.JSON should ignore C++11 users even though they make up half of the total possible userbase according to the latest JetBrains C++ language poll?

---

## Comment 2

> Username: wizard69  
> Created at: 2020-05-09 07:29:09 UTC  
> Url: https://github.com/boostorg/json/issues/87#issuecomment-626121784  

> So Boost.JSON should ignore C++11 users even though they make up half of the total possible userbase according to the latest JetBrains C++ language poll?  
  
Well if you put it that way it sounds unreasonable.   However that doesn’t reflect the reality of getting something into boost the eventually into the standard.  By the time that is done C++11 will be a distant memory.  It isn’t a question of the number of users now but rather the number in 3-6 years when the library is accepted.    
  
Beyond all of that I have no idea how Jet Brains collected their data on usage.  We might see half the world on something more recent so should the library suffer because half the world users can’t keep up?   So one; what Jet Brains think doesn’t mean much because I doubt it reflects the whole industry.   Second even if the numbers are accurate today, they will not be in 2021, less so in 2022 and so forth.     
  
To be honest I use Python more than C++ and saw the harm that the laggards created there refusing to move to Python 3.  The overall user base moved on and as a result new solutions ended up replacing those that could not move away from Python 2.   In the end I really believe new libraries should support the latest features (as is reasonable for a given project) of a language set and carefully consider the value in extreme backwards compatibility.  The Python 3 experience left me with a bad taste in my mouth with respect library developers not getting with the program.  This is different of course, but we are talking about a version of C++ standardized 10 years ago now.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-05-09 13:36:07 UTC  
> Updated at: 2020-05-09 13:36:45 UTC  
> Url: https://github.com/boostorg/json/issues/87#issuecomment-626177350  

You can't really compare Python 2.7 and Python 3 because there are incompatibilities. They are almost like two different languages.  
  
> I really believe new libraries should support the latest features (as is reasonable for a given project) of a language set and carefully consider the value in extreme backwards compatibility.   
  
Yes this is true, which is why I don't bother with C++03. Supporting C++11 is trivial. And nothing stops a library from conditionally taking advantage of newer C++ features if they are available. For example by conditionally adding constructor deduction guides (although in Boost.JSON this is not necessary since there are no class templates).

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-08-12 15:32:07 UTC  
> Updated at: 2020-08-12 15:32:23 UTC  
> Url: https://github.com/boostorg/json/issues/87#issuecomment-672946118  

I'm open to taking advantage of particular features in newer versions of C++, but they have to be enumerated. In other words, please open an issue describing the exact feature that you would like to see. General statements of the form "Boost.JSON should require a later version of C++" are unhelpful.  
  
That said, note that Boost.JSON _does_ support newer language features. For example, Boost.JSON supports C++17's structured bindings, see: https://github.com/CPPAlliance/json/commit/9fe41c51f007627cb36cdd6e9ff2e995f50122ee  
  
The implementation of this feature does not _require_ C++17, nor is it conditionally compiled based on the C++ language version. This is a perfect example of how Boost.JSON supports the most modern C++ features, while still requiring only C++11.  
  
I'm going to go ahead and close this since it is not actionable. If there are any specific C++ features you would like to see supported, please open an issue and describe the feature that you want.
