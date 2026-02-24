# #428 - Improving boost::compute::extents functionalities [Closed]

> Username: keryell  
> Created at: 2015-02-24 23:21:41 UTC  
> Updated at: 2017-03-22 17:20:23 UTC  
> Closed at: 2017-03-22 17:20:23 UTC  
> Url: https://github.com/boostorg/compute/issues/428  

By developing an application with the great Boost.Compute, I just figured out that even if extents looks like a STL container it is not even a limited one, which in unfortunate when writing something like this:  
  
  boost::compute::extents<n> global { ... };  
  boost::compute::extents<n> local { ... };  
  std::clog << "Launching kernel with { ";  
  for (auto v : global)  
    std::clog << v << ", ";  
  std::clog << "workitems with workgroups of { ";  
  for (auto v : local)  
    std::clog << v << ", ";  
  std::clog << "} workitems" << std::endl;  
because there is no begin()/end() method for example.  
  
If we change extents to inherit from array<> instead of having an array member it should work, such as:  
template<size_t N>  
class extents : public boost::array<size_t, N>;  
  
I made a similar approach for range<> and id<> in SYCL based on this class:  
https://github.com/keryell/triSYCL/blob/12055c1be2b243c82d4e45bfced9cac125a43e32/include/CL/implementation/sycl-implementation.hpp#L279  
I started to experiment it in Boost.Compute but I do not know how to write it in pre-C++11, without parent constructor inlining, etc... (SYCL requires C++11 at least, which may not be the case of Boost.Compute.)  
  
We can also add a lot of interesting extensions on boost::compute::extents, for example to compute the work-group geometry by just writing global/local, etc.  
  
Thank you.  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-02-25 18:08:42 UTC  
> Url: https://github.com/boostorg/compute/issues/428#issuecomment-76020137  

That's a good idea. I don't think inheritance is the right approach here, but I'll add `begin()` and `end()` methods to `extents<N>` which allow access to the underlying array values and enable the use of a for-range loop over an `extents<N>` object.

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-02-25 18:09:44 UTC  
> Url: https://github.com/boostorg/compute/issues/428#issuecomment-76020336  

Implemented in PR #429.
