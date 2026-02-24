# #6 Fix for BOOST_ALIGNMENT_OF being set to an "unknown" value. [Merged]

> Username: jzmaddock  
> Created at: 2015-02-21 18:15:03 UTC  
> Updated at: 2015-02-23 22:18:19 UTC  
> Merged at: 2015-02-23 22:18:19 UTC  
> Closed at: 2015-02-23 22:18:19 UTC  
> Url: https://github.com/boostorg/move/pull/6  

Leaving aside the wisdom of creating your own type_traits for a moment, if BOOST_ALIGNMENT_OF is set for a new compiler (for example the Solaris-12.4 compiler) then this code breaks.  The patch at least makes sure it compiles.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2015-02-23 22:17:48 UTC  
> Url: https://github.com/boostorg/move/pull/6#issuecomment-75649490  

Thanks John. Looking forward to the dependency-free version. It is wise to rectify ;-)

---
