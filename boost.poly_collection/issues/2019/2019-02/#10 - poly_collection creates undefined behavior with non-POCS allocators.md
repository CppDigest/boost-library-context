# #10 - poly_collection creates undefined behavior with non-POCS allocators [Closed]

> Username: BillyONeal  
> Created at: 2019-02-05 02:53:44 UTC  
> Updated at: 2019-02-05 20:35:52 UTC  
> Closed at: 2019-02-05 20:35:51 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/10  

This is detected by VS2019 RTM, and causes poly_collection tests to fail there.  
  
The test fails when rooted_allocator is set to no-propagate mode; so POCCA/POCMA/POCS do not propagate allocators, and thus on swap the allocators must be equal or you break http://eel.is/c++draft/container.requirements.general#9 :  
  
> If `allocator_­traits<allocator_­type>::propagate_­on_­container_­swap::value` is `true`, then [...]. Otherwise, the allocators shall not be swapped, and the behavior is undefined unless `a.get_­allocator() == b.get_­allocator()`.  
  
In test_construction.cpp, on lines 40 and 41, a rooted_poly_collection is created which is associated with the root1 allocator, associated with a const reference cp.  
On line 97, a rooted_poly_collection associated with root2 is created. (Note unequal allocators)  
On line 98, p2 is copy assigned over by cp.  
A few calls in, this ends up in type_info_map.hpp, which has:  
  
```  
  type_info_map& operator=(const type_info_map& x)  
  {  
    if(this!=&x){  
      type_info_map c{x};  
      swap(c);  
    }  
    return *this;  
  }  
```  
  
This makes a new container c with the root1 allocator, and then tries to swap with *this, which uses root2's allocator. If it's not a POCS allocator, that's UB as described above.  
  
I'm not sure how you want to fix the product code; this appears to be a regression in Boost 1.69 because 1.68 passed?

---

## Comment 1

> Username: BillyONeal  
> Created at: 2019-02-05 03:03:58 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/10#issuecomment-460496775  

(I think it's also detected by 2019 Preview 3; I'm not sure exactly which preview release has the change that detects this.)

---

## Comment 2

> Username: joaquintides  
> Created at: 2019-02-05 17:34:19 UTC  
> Updated at: 2019-02-05 17:52:50 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/10#issuecomment-460730217  

Hi Billy,  
  
Thanks for the report. After your submission of issue #9, I refactored the entire allocator-handling part of the lib so as to make it (to the best of my knowledge) fully allocator-aware (as per the standard). The current code for `type_info_map::operator=` looks like  
  
https://github.com/boostorg/poly_collection/blob/8a67013729862c1201eeba1859497959a36d2a41/include/boost/poly_collection/detail/type_info_map.hpp#L98-L111  
  
Maybe you can rerun your tests with the latest version from either develop or master and report your results? Thank you!

---

## Comment 3

> Username: BillyONeal  
> Created at: 2019-02-05 20:35:51 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/10#issuecomment-460793431  

I agree, that does look like it avoids the UB. Maybe the failure that put this on my radar was unrelated. I'll open a different issue with a different problem I found....
