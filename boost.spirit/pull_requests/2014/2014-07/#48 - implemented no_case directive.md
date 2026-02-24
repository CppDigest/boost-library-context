# #48 implemented no_case directive [Closed]

> Username: bylee20  
> Created at: 2014-07-16 15:13:32 UTC  
> Updated at: 2014-10-31 07:18:13 UTC  
> Closed at: 2014-10-31 07:18:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/48  

I'm not sure this is proper implementation.  
I've assumed that the type of dereferenced value from iterator is character. Is this ok?  
With help of boost::transform_iterator, transform each iterator value to lower/upper case.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-07-16 23:13:23 UTC  
> Updated_at: 2014-07-16 23:36:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/48#issuecomment-49240177  

This is not optimal. The idea is to inform the parsers to do the no-case themselves because only the parsers know how to implement them. The way I'd implement them is by adding a context flag that parsers such as lit, char_ and string_, symbols can detect and dispatch to the right code appropriately. If you want to do a proper implementation, we can discuss this over at IRC, or the spirit list perhaps. Ping me.

---
