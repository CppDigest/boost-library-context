# #233 - RTTI-disabled builds. [Closed]

> Username: psiha  
> Created at: 2025-05-05 13:09:16 UTC  
> Updated at: 2025-07-12 20:25:46 UTC  
> Closed at: 2025-07-12 20:25:46 UTC  
> Url: https://github.com/boostorg/parser/issues/233  

Due to the use of std::any the library requires RTTI (needles to say, which is a problem for some:).  
  
  
Which brings us back to the symbol map issue raised before (where std::any is used):  
> A map is used for the symbol table logic. If you con't use symbol tables, that map remains empty -- no allocations should occur.  
  
MS STL allocates even for an empty std::map and you have two of those so thats two allocations at minimum. And again skip() @ parser.hpp@1522 creates two of those again in each call (even if symbols aren't used/'mentioned' anywhere in the source).  
  
Beyond this we still get the map, any, trie...code (including related EH codegen) in the binary. As far as I can remember, I don't remember seeing this in X3 codegen (and it certainly does not need RTTI for symbol tables).  
Ideally we'd come up with a way to honor the "don't pay for it if you don't use it" at compiletime, or (with this being near impossible even when the entire expression is in a single TU as soon as rules enter the picture) at least a slimmer implementation that the optimizer can trim as much as possible in O3 LTO builds)...

---

## Comment 1

> Username: psiha  
> Created at: 2025-05-06 10:02:26 UTC  
> Url: https://github.com/boostorg/parser/issues/233#issuecomment-2853976940  

> Ideally we'd come up with a way to honor the "don't pay for it if you don't use it" at compiletime,  
  
Could you make the symbol parser objects themselves precompile and hold this data?

---

## Comment 2

> Username: tzlaine  
> Created at: 2025-07-12 20:25:46 UTC  
> Url: https://github.com/boostorg/parser/issues/233#issuecomment-3066026215  

People that turn off RTTI are, definitionally, not using C++.  This is a C++ library.  
  
That being said, I have had to deal with such environments before.  It is possible to make a `std::any`-alike that does not use RTTI.  It is also possible to use a `std::map` that doesn't allocate (like all the other implementations besides MSVC).  
  
I'm not going to write these replacements, because I don't see a lot of need for them.  If you or someone else were to provide high-quality, portable implementations with good test coverage, I would use them.  
  
If you or someone else does that implementation work, please re-open this ticket.
