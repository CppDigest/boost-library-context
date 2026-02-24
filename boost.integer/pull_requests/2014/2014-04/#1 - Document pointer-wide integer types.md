# #1 Document pointer-wide integer types [Closed]

> Username: Lastique  
> Created at: 2014-04-13 13:01:11 UTC  
> Updated at: 2015-01-25 17:14:26 UTC  
> Closed at: 2015-01-25 17:14:26 UTC  
> Url: https://github.com/boostorg/integer/pull/1  

#9841. Added documentation for intptr_t, uintptr_t and BOOST_HAS_INTPTR_T. Also fixed formatting in a few places.

---

## Comment 1

> Username: viboes  
> Created_at: 2015-01-25 15:13:48 UTC  
> Url: https://github.com/boostorg/integer/pull/1#issuecomment-71376774  

This should be moved to Config as   
  
```  
Removed from library: Standard Integer Types  
  
The Boost.Config module provides the typedefs useful for writing portable code that requires certain integer widths.   
```

---

## Comment 2

> Username: pabristow  
> Created_at: 2015-01-25 16:06:51 UTC  
> Url: https://github.com/boostorg/integer/pull/1#issuecomment-71378845  

I am busy revising the Boost.Integer docs and already have this in the text.  
  
Also a note warning about this in the release notes.  
  
I’ll release my efforts for review in the next few days.  
  
Paul  
  
PS We also need some big warnings about the move of all other items in the Integer library.  
  
This move is going to break some user code (apart from Boost.Multiprecision), I fear.  
  
---  
  
Paul A. Bristow  
  
Prizet Farmhouse  
  
Kendal UK LA8 8AB  
  
+44 (0) 1539 561830  
  
From: Vicente J. Botet Escriba [mailto:notifications@github.com]   
Sent: 25 January 2015 15:14  
To: boostorg/integer  
Subject: Re: [integer] Document pointer-wide integer types (#1)  
  
This should be moved to Config as   
  
Removed from library: Standard Integer Types  
  
The Boost.Config module provides the typedefs useful for writing portable code that requires certain integer widths.   
  
—  
Reply to this email directly or view it on GitHub https://github.com/boostorg/integer/pull/1#issuecomment-71376774 .  https://github.com/notifications/beacon/ABBuATwChIjnCdFogCJNG8sLyNKkjRLqks5nlP-sgaJpZM4ByBcZ.gif

---

## Comment 3

> Username: Lastique  
> Created_at: 2015-01-25 16:12:11 UTC  
> Url: https://github.com/boostorg/integer/pull/1#issuecomment-71379081  

So should I withdraw this pull request and not move it to Config?

---

## Comment 4

> Username: viboes  
> Created_at: 2015-01-25 16:37:22 UTC  
> Url: https://github.com/boostorg/integer/pull/1#issuecomment-71380161  

I suspect that it would be better to withdraw it.

---
