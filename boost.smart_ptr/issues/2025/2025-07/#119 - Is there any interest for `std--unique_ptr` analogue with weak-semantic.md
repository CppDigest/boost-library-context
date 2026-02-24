# #119 - Is there any interest for `std::unique_ptr` analogue with weak-semantic? [Open]

> Username: denzor200  
> Created at: 2025-07-03 23:55:10 UTC  
> Updated at: 2025-07-03 23:55:10 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/119  

**Hi everyone,**  
  
I’ve implemented a weak-reference-enabled version of `std::unique_ptr`, called `weakable_unique_ptr`. The core idea is to combine the strict ownership semantics of `std::unique_ptr` with the ability to create non-owning observers (called `unique_weak_ptr`), _without_ introducing shared ownership or thread-safety overhead.  
  
**Key Properties**  
- **Exclusive Ownership**: The owned object is destroyed when the `weakable_unique_ptr` is destroyed (like `std::unique_ptr`);  
- **Observation**: `unique_weak_ptr` observers automatically expire when the owned object is destroyed (like `std::weak_ptr`);  
- **Less cost**: no copy operation for `weakable_unique_ptr` at all, `unique_weak_ptr`'s reference counter without atomic operations.  
  
**Implementation Status**  
Attached is a working prototype of `weakable_unique_ptr` and `unique_weak_ptr`, tested on Godbolt's GCC 15.1.  
The current implementation is standalone (no modifications to Boost.SmartPtr library required) and supports:  
- Move-only semantics (like `std::unique_ptr`);  
- Safe validity checks via `try_get()` or `expired()`.  
  
The current implementation doesn't support yet:  
- Handling array types;  
- Handling deleter as reference;  
- EBO for deleter;  
- Lazy initalization for control block(BTW do we really need to support it?);  
- Type erasing for control block(in order not to pollute binary);  
- Construction and assignment from `std::unique_ptr`;  
- `operator[]` for array types;  
- implicit conversion to `std::shared_ptr`.  
  
**Use Case Example**  
```  
struct Node {   
    boost::weakable_unique_ptr<Node> child;   
    boost::unique_weak_ptr<Node> parent; // Safe back-reference   
};   
  
void demo() {   
    auto root = boost::make_weakable_unique<Node>();   
    auto child = boost::make_weakable_unique<Node>();   
    child->parent = root; // Safe link   
    root->child = std::move(child);   
} // All auto-destructs, no leaks   
```  
This is useful for:  
- Trees/ASTs with parent links.  
- GUI hierarchies (e.g., widgets owning children but needing parent access).  
- Any graph structure where ownership is linear but observation is needed.  
  
**Next Steps**  
If there’s interest, I’d love to:  
1. Polish the code to Boost standards.  
2. Add exhaustive tests/docs.  
3. Integrate with Boost.SmartPtr library.  
  
**Questions for the community:**  
- Does this align with Boost’s goals?  
- Should it live under Boost.SmartPtr library or as a standalone utility?  
- Any design concerns?  
  
[weakable_unique_ptr](https://github.com/denzor200/weakable_unique_ptr_prototype/blob/main/weakable_unique_ptr.hpp)  
The full snippet to play: https://godbolt.org/z/41535cTvo
