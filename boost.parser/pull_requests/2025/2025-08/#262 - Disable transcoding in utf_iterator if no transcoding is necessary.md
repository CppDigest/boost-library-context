# #262 Disable transcoding in utf_iterator if no transcoding is necessary. [Closed]

> Username: andreasbuhr  
> Created at: 2025-08-06 09:28:43 UTC  
> Updated at: 2026-01-26 14:16:00 UTC  
> Closed at: 2026-01-26 14:16:00 UTC  
> Url: https://github.com/boostorg/parser/pull/262  

Another option to implement  
https://github.com/boostorg/parser/issues/260

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-10-13 02:27:54 UTC  
> Url: https://github.com/boostorg/parser/pull/262#issuecomment-3395666473  

This is the kind of thing I'd like to avoid -- it's pretty complicated for what it does.  For instance, the `FromFormat == ToFormat` and `FromFormat != ToFormat` cases have different invariants.  The read position is before the current cp for the former, and after it for the latter.  I think it would be clearer to just pick the right iterator in the first place.  IOW, the equivalent to the `FromFormat != ToFormat` check should happen at the point where we choose whether to use utf_iterator or not.

---
