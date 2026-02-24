# #93 Xml nullkey [Open]

> Username: brchrisman  
> Created at: 2021-08-28 06:00:21 UTC  
> Updated at: 2023-11-22 13:57:43 UTC  
> Url: https://github.com/boostorg/property_tree/pull/93  

Got back to tracking down this type issue of char vs wchar_t.  Patch doesn't cast unless a null key is found.

---

## Comment 1

> Username: ashtum  
> Created_at: 2023-11-17 06:58:27 UTC  
> Url: https://github.com/boostorg/property_tree/pull/93#issuecomment-1815836728  

@brchrisman, could you please provide some background on this? What issue is it designed to address?

---

## Comment 2

> Username: brchrisman  
> Created_at: 2023-11-17 11:49:27 UTC  
> Url: https://github.com/boostorg/property_tree/pull/93#issuecomment-1816249639  

> @brchrisman, could you please provide some background on this? What issue is it designed to address?  
  
A property-tree with nulls as keys serializes to broken XML (having null tag names like <>foo</>).  
This patch places a nonce in there that is placed in serialize and elided in unserialize.

---
