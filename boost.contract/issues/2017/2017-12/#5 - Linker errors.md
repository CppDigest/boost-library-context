# #5 - Linker errors [Closed]

> Username: skuma177  
> Created at: 2017-12-07 02:59:26 UTC  
> Updated at: 2017-12-11 01:50:26 UTC  
> Closed at: 2017-12-11 01:50:26 UTC  
> Url: https://github.com/boostorg/contract/issues/5  

I am trying to use the library in my project and getting link errors if I include contract.hpp in more than one compilation units. Reason for which is free functions in some files do not have local linkage and they end up violating ODR.  
  
One such example is boost::contract::null_old() in boost/contract/old.hpp  
  
old_value null_old() { return old_value(); }

---

## Comment 1

> Username: lcaminiti  
> Created at: 2017-12-07 06:31:12 UTC  
> Url: https://github.com/boostorg/contract/issues/5#issuecomment-349876173  

Yes, thanks for catching this. I fixed ODRs a while back but it looks like I missed the functions in old.hpp: null_old, copy_old, and make_old. The rest of the library seems fine. I'll fix this properly in the next couple of weeks.  
  
In the meanwhile you can workaround this bug by declaring these functions inline if you want:  
  
old.hpp:673:inline old_value null_old() { return old_value(); }  
old.hpp:700:inline old_pointer make_old(old_value const& old) {  
old.hpp:733:inline old_pointer make_old(virtual_* v, old_value const& old) {  
old.hpp:751:inline bool copy_old() {  
old.hpp:784:inline bool copy_old(virtual_* v) {

---

## Comment 2

> Username: lcaminiti  
> Created at: 2017-12-11 01:50:26 UTC  
> Url: https://github.com/boostorg/contract/issues/5#issuecomment-350603027  

I fixed this moving the definitions for these functions to contract/detail/inlined/old.hpp. Only old.hpp was missing inlined definitions (other definitions were already inlined). I also added two tests for ODR errors (in test/disable).
