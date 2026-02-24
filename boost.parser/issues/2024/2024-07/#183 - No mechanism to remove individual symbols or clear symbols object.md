# #183 - No mechanism to remove individual symbols or clear symbols object. [Closed]

> Username: vmauery  
> Created at: 2024-07-28 22:13:08 UTC  
> Updated at: 2024-10-03 22:56:01 UTC  
> Closed at: 2024-10-03 22:56:01 UTC  
> Url: https://github.com/boostorg/parser/issues/183  

I have a usage where the symbols needs to be dynamic (add and remove). It is possible to remove them one by one to rebuild the symbols table, but it would be most convenient to just call symbols.clear() and then add back the new symbols.

---

## Comment 1

> Username: vmauery  
> Created at: 2024-07-28 22:37:20 UTC  
> Url: https://github.com/boostorg/parser/issues/183#issuecomment-2254673318  

I found I was able to clear the symbol table with     symbols.parser_.initial_elements_.clear(); But I fear that this is cheating, using what looks like what would be private/protected elements without knowing the side effects. Also, the interface might be gone when this goes into Boost. That would break things for me again. Please add this clear mechanism.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-09-29 21:34:16 UTC  
> Url: https://github.com/boostorg/parser/issues/183#issuecomment-2381618745  

Yeah, reaching into the implementation details like that is definitely not stability-guaranteed.  
  
This seems like a useful thing to add.  I think we're also missing the ability to erase from subsequent parses (right now you can add to this parse, or subsequent parses, but cannot remove entries for a subsequent parse).  This also suggests we need `clear()` and `clear_for_next_parse()`.  I think the one you're asking for is the latter, but I think both are potentially useful.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-09-30 20:41:13 UTC  
> Url: https://github.com/boostorg/parser/issues/183#issuecomment-2384107547  

I'm so glad you asked for this.  The extra tests I wrote for the new API have revealed a pretty serious bug in the way the `*_for_next_parse()` overloads worked.  In particular, if used on symbols object `S` before the first time `S` is used to parse within a given top-level parse, they applied *to the current top-level parse*, not just the next one.  This was not the case if `S` is used to parse  and *then* you use the `*_for_next_parse()` API.  I'm fixing it now.
