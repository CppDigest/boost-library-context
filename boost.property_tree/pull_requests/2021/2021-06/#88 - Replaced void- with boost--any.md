# #88 Replaced void* with boost::any [Closed]

> Username: yost-jacob  
> Created at: 2021-06-11 22:04:43 UTC  
> Updated at: 2023-12-07 13:03:19 UTC  
> Closed at: 2023-12-07 13:03:19 UTC  
> Url: https://github.com/boostorg/property_tree/pull/88  

I think this would be a great improvement to the library.  
  
Makes default/implicitly created copy constructors well behaved  
Implicit destructor now also works  
which makes move constructor available and implicitly created  
by using the any we are compliant with the rule of 0  
  
Note on moved from state:  
Move operations move the contents of the any, and not necessarily the container it contains.  So assignment operators and clear would return it to a specified state.  Which is to my understanding the requirement for "valid but unspecified".  All other operations will throw.  
clear() reconstructs the child container if not present.  
  
If more functionality is desired post-move we would have to declare/define all the constructors and assignment operators.  Alternatively, like the note about ctors from the original maintainers we could make the child container on demand, which isn't as difficult with the any.  I left some commented out lines which would probably capture most of it.  
  
It shouldn't have any outward functional changes best I can tell.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-06-12 10:45:26 UTC  
> Url: https://github.com/boostorg/property_tree/pull/88#issuecomment-860035779  

Thank for the PR. I’ll take a look.

---

## Comment 2

> Username: ashtum  
> Created_at: 2023-12-07 12:55:26 UTC  
> Url: https://github.com/boostorg/property_tree/pull/88#issuecomment-1845294404  

@yost-jacob, as you're aware, the `void*` here is used because it wasn't feasible to complete the `boost::multi_index_container` type within the `basic_ptree` itself (which uses `basic_ptree` itself as the value type). Consequently, we can always be certain that `void* m_children` points to an instance of `subs::base_container`, and it is never null. With these assurances, this appears to be a legitimate use of `void*`, and adopting `std::any` might be overkill.

---
