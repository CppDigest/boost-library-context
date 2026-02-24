# #37  Fix mpl::has_key_impl<parameter::aux::arg_list_tag> specialization [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-29 19:32:50 UTC  
> Updated at: 2018-12-02 11:22:29 UTC  
> Merged at: 2018-12-02 06:37:04 UTC  
> Closed at: 2018-12-02 06:37:04 UTC  
> Url: https://github.com/boostorg/parameter/pull/37  

<boost/parameter/aux_/arg_list.hpp>  
* Use parameter::value_type vice mpl::find.  
  
"test/compose.cpp"  
* Test that mpl::has_key evaluates to mpl::false_ for keyword tags that are not in parameter::aux::arg_list.  
  
"test/singular.cpp"  
* Test that mpl::has_key evaluates to mpl::false_ for keyword tags that are not in parameter::aux::tagged_argument or parameter::aux::tagged_argument_rref.

---
