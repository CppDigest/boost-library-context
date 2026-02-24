# #620 Minimax and distribution_explorer to examples. [Merged]

> Username: NAThompson  
> Created at: 2021-04-30 17:42:09 UTC  
> Updated at: 2021-05-03 13:13:02 UTC  
> Merged at: 2021-05-03 13:12:59 UTC  
> Closed at: 2021-05-03 13:12:59 UTC  
> Url: https://github.com/boostorg/math/pull/620  

And while we're at it: What do these do? I've never seen these run or tested . . .

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-05-02 09:20:10 UTC  
> Url: https://github.com/boostorg/math/pull/620#issuecomment-830777724  

The Remez code is documented here: https://www.boost.org/doc/libs/1_42_0/libs/math/doc/sf_and_dist/html/math_toolkit/toolkit/internals2/minimax.html  
  
I'd rather not loose it, since it contains the record of how the minimax approximations were derived (and is the starting point for new ones).  It could be moved under tools/minimax if we want to tidy up the root directory.  
  
The distribution-explorer code is a .NET application that @pabristow wrote to illustrate the behaviour of our distribution classes.  I confess I'm unable to find any docs on it - Paul?

---

## Comment 2

> Username: pabristow  
> Created_at: 2021-05-02 10:26:30 UTC  
> Url: https://github.com/boostorg/math/pull/620#issuecomment-830786157  

@jzmaddock - I deny writing it! (My eyes are dim, I cannot see ... ... ) But I may have influenced its production.  I have no reason to believe that it no longer works and it may still be a useful guide to anyone producing C# / .NET code that uses Boost.Math. Was one original purpose to show that this was possible?  
  
Docs are at distexplorer.qbk and don't look too out of date.   
  
It's not hurting anyone?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-05-02 17:41:57 UTC  
> Url: https://github.com/boostorg/math/pull/620#issuecomment-830844667  

>  It could be moved under tools/minimax if we want to tidy up the root directory.  
  
Done.  
  
> It's not hurting anyone?  
  
Just moved to the `examples` directory-not deleted.

---
