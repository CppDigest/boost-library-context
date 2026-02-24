# #82 - compile error in function.hpp: error: invalid use of incomplete type ‘struct boost::mpl::size<blahblahblah>' [Closed]

> Username: etam  
> Created at: 2014-04-16 23:44:42 UTC  
> Updated at: 2014-04-17 03:48:17 UTC  
> Closed at: 2014-04-17 02:14:12 UTC  
> Url: https://github.com/boostorg/compute/issues/82  

mpl::size is used in make_function_declaration.  
Adding "#include <boost/mpl/size.hpp>" to function.hpp fixes the problem.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-04-17 02:14:12 UTC  
> Url: https://github.com/boostorg/compute/issues/82#issuecomment-40673914  

Thanks for the report!  
  
Fixed in: b3ab165

---

## Comment 2

> Username: roshanrags  
> Created at: 2014-04-17 03:25:45 UTC  
> Url: https://github.com/boostorg/compute/issues/82#issuecomment-40677318  

Shouldn't Travis have caught this esp. since Coveralls says the function was covered while testing?

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-04-17 03:48:17 UTC  
> Url: https://github.com/boostorg/compute/issues/82#issuecomment-40678238  

It's probably due to the fact that one of the other `boost/mpl` headers we include (either `boost/mpl/for_each.hpp` or `boost/mpl/transform.hpp`) used to bring in `boost/mpl/size.hpp` but no longer does (e.g. changed in different boost versions or different configurations). Now we explicitly include it (as we should have before) to make sure that `mpl::size<>` is always available.
