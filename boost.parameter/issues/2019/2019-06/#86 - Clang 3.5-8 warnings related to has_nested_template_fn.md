# #86 - Clang 3.5-8 warnings related to has_nested_template_fn [Closed]

> Username: joaquintides  
> Created at: 2019-06-27 11:14:59 UTC  
> Updated at: 2019-06-29 08:54:43 UTC  
> Closed at: 2019-06-29 08:54:43 UTC  
> Url: https://github.com/boostorg/parameter/issues/86  

As shown in Boost.Flyweight [regression tests](https://www.boost.org/development/tests/develop/output/teeks99-dkr-dc3-5-14-flyweight-clang-linux-3-5~c++14-warnings.html#test_assoc_cont_factory):  
```  
test_assoc_cont_factory  
  
../boost/parameter/aux_/has_nested_template_fn.hpp:39:64:   
  warning: inline function  
    'boost::parameter::aux::has_nested_template_fn_impl<  
       boost::flyweights::detail::is_tag<mpl_::arg<-1> >   
    >::_check<boost::flyweights::detail::is_tag<mpl_::arg<-1> > >' is not defined  
  [-Wundefined-inline]  
  
...  
```  
This spurious warning is connected with the `constexpr` specification of the various `_check` function templates. Problem reproduced [here](https://wandbox.org/permlink/v1DPPzej6u9r2ked). I guess the fix is as simple as dropping `BOOST_CONSTEXPR`, since after all it isn't (AFAICS) needed.

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-06-29 08:54:43 UTC  
> Url: https://github.com/boostorg/parameter/issues/86#issuecomment-506941170  

Thank you!
