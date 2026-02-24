# #17 - [peer-review] Clarify or Remove Container-Like Features (emplace, allocator semantics) [Closed]

> Username: Becheler  
> Created at: 2025-05-31 11:40:14 UTC  
> Updated at: 2025-06-13 11:58:17 UTC  
> Closed at: 2025-06-13 11:58:17 UTC  
> Url: https://github.com/boostorg/bloom/issues/17  

# Description   
  
Several reviewers on the ML and Slack noted that certain API names and parameters imply “container” semantics (emplace and allocators) and can mislead users: below are direct quotes from the review process.  
  
## Emplace semantics  
  
**Ruben Perez, May 21:**    
 - > Does filter::emplace() add any value over just constructing and inserting an object? In a traditional container, this is justified because the elements are actually stored, but that does not seem to be the case here.  
  
**Vinnie Falco, May 22:**  
- > I'm puzzled about the terminology. "insert" and "emplace" imply that new nodes or array elements of type value_type are being stored. This is not the case though, as these objects are hashed and the resulting digest is used to algorithmically adjust the filter which is in essence an array of bool. `emplace` documentation says that it constructs elements.   
  
**Seth, May 23:**    
- > In other words, it *doesn't emplace*.    
- > `emplace` to me always meant 'inplace-construction' (for node-based containers, `std::optional`, `std::any`, `std::variant`). If `emplace` by definition destructs the element constructed, should it have a different name?  
- > I think I agree with those that think the adherence to container-like member names does more harm than good. `insert` is fine, because in _some sense_ (potential set membership) an element is being inserted. For the current semantics, `emplace` should be something like `insert_from_temporary`, but I don't see the appeal over `insert(T(...))`.  
  
**David Bien :  
-  > (ML, May 23) I think ridding emplace entirely is probably the best move - we aren’t emplacing anything in reality  
- >  (Personal, May 25) not overly concerned that emplace() feels somewhat contrived here (mainly a convenience). Some team members reading the code superficially might find it a bit confusing, but it does not warrant removal.  
  
## Allocator Parameters  
  
**Seth, May 23:**    
- > The uses-allocator argument just highlights to me that having the allocator be `allocator<T>` instead of `allocator<unsigned char>` feels misleading. My understanding is that `bloom::filter` in principle never stores `T`.  
- > I prefer the caller be in charge of any allocators used for temporaries of `T`. It's actually more likely that it would *not* coincide with the allocator for the filter array (e.g., a stack/pool allocator for repeated temps; bloom filters themselves typically have longer lifetimes and fixed capacities, the polar opposite usage pattern).  
- > Also, having the temporary right there in the interface (`insert(T(...))`) makes the [allocation] cost visible to the user. It might prompt the user to switch to a custom `Hash` type that doesn't incur the [same] cost.  
  
---  
  
### Problem Summary  
1. `emplace()` vs. `insert()`:  In standard containers, `emplace()` constructs an element directly in the container’s storage. In Boost.Bloom, however, `emplace()` simply builds a temporary, hashes it, then discards it—exactly the same as doing `insert(T{…})`. This can mislead readers (especially on a team) into thinking that a `T` is being stored in place, when in fact only bits are updated.  
2. Allocator Template/Parameter: Accepting an `allocator<T>` suggests that the filter will allocate memory for `T` objects. In reality, a Bloom filter never holds `T` directly—only a bit array. As a result, passing an allocator for `T` has no effect on per-element storage and may confuse users about where memory is used.  
  
### Possible Resolution  
  
  1. **Remove Allocator Template/Parameter**: If it serves no practical purpose.    
  2. **Clarify in Documentation**: Explain that the allocator is unused for per-element storage and exists only to match Boost containers’ interface patterns.  
  
## Action Items  
  
1. **Review `emplace()` Implementation**    
   - [x] Decide whether to remove or keep `emplace()`.    
   - [ ] If keeping, update documentation to explicitly state that `emplace()` does not perform true in-place construction or store `value_type`.  
  
2. **Review Allocator Usage**    
   - [x] Determine if the allocator parameter can be removed without breaking user code.    
   - [ ] If retained, add documentation clarifying that no per-element allocation occurs and the allocator is effectively unused.  
  
3. **Update Documentation**    
   - [ ] Add a note in the API reference for both `emplace()` and `insert()`, explaining their identical behavior.    
   - [ ] Clearly state why allocator support exists (e.g., “provided for interface consistency, but no `T` objects are stored”).  
  
Thank you!

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-13 08:27:09 UTC  
> Url: https://github.com/boostorg/bloom/issues/17#issuecomment-2969524063  

* [Removed emplace](https://github.com/joaquintides/bloom/commit/2b52cab49cacc6d512609ed04f66b3747cddf21e)  
* [Required the allocator to be of unsigned char](https://github.com/joaquintides/bloom/commit/ddce6958ddf568bce49193aa84e67670484e5438)

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-13 11:58:17 UTC  
> Url: https://github.com/boostorg/bloom/issues/17#issuecomment-2970168225  

Farewell, container semantics ! 😢   
Let's hope it does not come back as a user feature whishlist 😸   
Thanks for updating the code!
