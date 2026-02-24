# #765 - Discuss and remove std::is_floating_point specialization [Open]

> Username: sdebionne  
> Created at: 2025-04-22 07:04:28 UTC  
> Updated at: 2025-04-22 07:04:28 UTC  
> Url: https://github.com/boostorg/gil/issues/765  

Just so that this discussion doesn't get lost -event if #764 seems to fix the compile issue.  
  
The only place where `is_floating_point` is used within GIL seems to be the TIFF I/O extension, and there it's only an implementation detail: https://github.com/boostorg/gil/blob/688acf78f7fc4ecb5b1c6f1e4a8f4a5939d28e9b/include/boost/gil/extension/io/tiff/detail/is_allowed.hpp#L101C13-L101C30  
  
Furthermore I'm a bit worried, because [cppreference.com states](https://en.cppreference.com/w/cpp/types/is_floating_point):  
  
> If the program adds specializations for `std::is_floating_point` or `std::is_floating_point_v`, the behavior is undefined.  
  
Doesn't sound like this is something one should do. As long as those lines were in the `boost` namespace (before the PR), that was not a specialization of `std::is_floating_point` but `boost::is_floating_point`, a different thing - although probably with similar intentions. But with the move to the `std` namespace it now is a specialization.  
  
_Originally posted by @striezel in https://github.com/boostorg/gil/issues/761#issuecomment-2781161234_
