# #91 Removed empty user defined dtor [Closed]

> Username: Flast  
> Created at: 2018-11-22 11:34:20 UTC  
> Updated at: 2018-11-24 13:35:34 UTC  
> Closed at: 2018-11-24 12:25:33 UTC  
> Url: https://github.com/boostorg/filesystem/pull/91  

since copy/move ctor/assign are implicitly deleted.  
  
GCC warns about the deprecated behaviour: https://wandbox.org/permlink/ESjczTTSMK0MTiyh .

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-11-24 12:25:33 UTC  
> Url: https://github.com/boostorg/filesystem/pull/91#issuecomment-441364401  

Fixed in https://github.com/boostorg/filesystem/commit/498a090b531833355ea27cea94d0440c432f8afc, thanks.

---
