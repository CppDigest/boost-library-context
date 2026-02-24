# #9 - Isn't C++20's view_interface in namespace std::ranges? [Closed]

> Username: hadrielk  
> Created at: 2019-12-17 23:23:32 UTC  
> Updated at: 2019-12-29 19:54:21 UTC  
> Closed at: 2019-12-29 19:54:21 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/9  

Currently view_interface.hpp says this:  
https://github.com/tzlaine/stl_interfaces/blob/c47f91507e75574278e0167accdcb28a0d1bf136/include/boost/stl_interfaces/view_interface.hpp#L212  
  
...but my reading of the current C++20 draft [N4842](https://wg21.link/N4842) shows it to be in `std::ranges`, not `std`.  
  
Of course I could easily be wrong, and if so then my apologies.

---

## Comment 1

> Username: tzlaine  
> Created at: 2019-12-18 02:10:04 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/9#issuecomment-566834476  

No, you're absolutely right!  I just never had the chance to actually use this code, since I don't have access to a `std::ranges` implementation of `view_interface` yet.  Thanks for pointing this out.
