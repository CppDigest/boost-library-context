# #37 - Moved-from allocator is copied and used when move-constructing multi_index_container [Closed]

> Username: zabereer  
> Created at: 2020-09-29 06:27:43 UTC  
> Updated at: 2020-09-29 19:20:38 UTC  
> Closed at: 2020-09-29 19:20:37 UTC  
> Url: https://github.com/boostorg/multi_index/issues/37  

When using a custom allocator that contains say a smart pointer to a resource manager then moving the allocator results in the smart pointer being a nullptr due to it being moved. Taking copies of this moved-from allocator results in unusable allocators due to the nullptr to a resource manager.  
  
The move-constructor of multi_index_container moves the allocator but then copies the moved-from allocator into the indexes.

---

## Comment 1

> Username: joaquintides  
> Created at: 2020-09-29 08:05:19 UTC  
> Url: https://github.com/boostorg/multi_index/issues/37#issuecomment-700525071  

Hi, thanks for the report.  
  
As clarified in [LWG2593](https://cplusplus.github.io/LWG/issue2593), allocator move construction is required to *not* change the moved-from allocator instance. The way I read it, an allocator such as you describe would simply be non-conformant.  
  
Opinions?

---

## Comment 2

> Username: zabereer  
> Created at: 2020-09-29 08:58:19 UTC  
> Url: https://github.com/boostorg/multi_index/issues/37#issuecomment-700566788  

Ah of course - because they have to compare equal even after move.  
In my opinion it would be better if the standard always let move mean "move" and disallow containers to move their allocators. I am not sure if that is practical. I would be interested in your opinion about this.  
And thank you for pointing this out to me.

---

## Comment 3

> Username: joaquintides  
> Created at: 2020-09-29 14:36:15 UTC  
> Updated at: 2020-09-29 14:39:17 UTC  
> Url: https://github.com/boostorg/multi_index/issues/37#issuecomment-700749089  

From what I've gathered reading the available material, LWG2593 proposed correction was basically accepted because a moved-from container might still need to delete internal nodes and stuff upon destruction, which means it needs a functional allocator at that point.  
  
In hindsight, it'd have been better to require that containers always copy allocators on container move copy/assignment, but I guess they went for the minimum change in the spec that fixed the problem. Not that it has great practical implications, but you can have different allocator copy and move construction code as long as the allocator state is preserved --you can use that leeway for instrumentation purposes, for instance.

---

## Comment 4

> Username: zabereer  
> Created at: 2020-09-29 19:20:37 UTC  
> Url: https://github.com/boostorg/multi_index/issues/37#issuecomment-700929099  

Thank you for explaining.
