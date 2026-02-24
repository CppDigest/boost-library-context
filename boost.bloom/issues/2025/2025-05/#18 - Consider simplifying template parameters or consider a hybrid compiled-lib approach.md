# #18 - [peer-review] Consider simplifying template parameters or consider a hybrid compiled-lib approach [Closed]

> Username: Becheler  
> Created at: 2025-05-31 17:04:29 UTC  
> Updated at: 2025-06-13 11:54:32 UTC  
> Closed at: 2025-06-13 11:54:32 UTC  
> Url: https://github.com/boostorg/bloom/issues/18  

This issue records the review explorations and discussions of a heavily templated versus a compiled-library approach. It represents significant extra effort and should be viewed primarily as an exploratory, historical reference.  
  
## Description:  
  
On Slack Vinnie Falco suggested exploring a design that starts with a compiled library (instead of heavy templating) and only adds templates when performance or flexibility justifies them. Below are his original comments from Slack (May 25th, 6:32)  
  
- > “This is just my opinion, and I think it is better to start by designing for a compiled lib and only start making things templates when evidence suggests that doing so is a better choice.”    
- > Maybe for Bloom, the templates are always the better choice. I don't know, because I have not looked into it deeply and I did not write the lib. Yet do we really know?  
- > Maybe I'm wrong. Yet it seems to me that once you compute the hash you have the operation of setting the bit in the filter and that doesn't immediately jump out to me as requiring template parameters.  
  
---  
  
## Issue Summary  
  
Implementing too many templates can increase compile times, complicate diagnostics, and burden users. If certain core components (e.g., the bit‐array manipulation and hash application logic) can live in a compiled, non‐templated library, then templating could be used only for high‐level customization (e.g., custom hash functors or allocator types).   
  
## Discussion Points to document  
  
1. **Template Needs**    
   - Identify which templates (`<T, Hash, BucketSize>`, etc.) are essential and if runtime parameters suffice.  
  
4. **Community Input**    
   - Gather feedback on compile-time issues and whether an alternative model helps or hinders.  
     
2. **Compiled-Core Idea**    
   - Sketch a minimal API (create/insert/lookup) to evaluate an alternative design feasability (maybe it's just not possible)  
  
3. **Trade-Offs**    
   - Since benchmarks rule, possibly compare compile time, binary size, and user convenience between fully-templated and hybrid designs (although that sounds like a lot of work and requires the actual alternative implementation)  
  
---  
  
## Next Steps  
  
- Record benchmark and feedback findings.    
- Choose to:    
  1. Keep fully-templated (with rationale and informed decisions).    
  2. Build a compiled core with thin template wrappers.    
  3. Remove unnecessary templates without full refactor.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-13 08:44:22 UTC  
> Url: https://github.com/boostorg/bloom/issues/18#issuecomment-2969568012  

* The only template parameter that could potentially be eliminated would be the allocator in favor of `std::pmr::polymorphic_allocator` or similar, and I haven't seen any evidence of that helping compile times in any way.  
* The library already sports a two-layer architecture to reduce codegen as much as possible: `filter` (dependent on `T`, `K`, `Subfilter`, `BucketSize`, `Hash` and `Allocator`) is implemented in terms of `filter_core` (dependent on `K`, `Subfilter`, `BucketSize` and `Allocator`). I don't see what extra steps could be taken in this direction.  
  
For these reasons, my preferred course of action is to do nothing else.

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-13 11:54:32 UTC  
> Url: https://github.com/boostorg/bloom/issues/18#issuecomment-2970160320  

Very well, I can close this issue then ! Thanks for the summary 😄
