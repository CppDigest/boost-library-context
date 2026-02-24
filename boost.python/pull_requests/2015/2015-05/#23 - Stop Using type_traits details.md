# #23 Stop Using type_traits details. [Merged]

> Username: jzmaddock  
> Created at: 2015-05-21 18:20:40 UTC  
> Updated at: 2015-05-27 15:07:30 UTC  
> Merged at: 2015-05-27 15:07:30 UTC  
> Closed at: 2015-05-27 15:07:30 UTC  
> Url: https://github.com/boostorg/python/pull/23  

The type_traits internal details used here are being moved to make it clear they are details and subject to change.  Old code will continue to work, but will warn about use of a deprecated header.

---
