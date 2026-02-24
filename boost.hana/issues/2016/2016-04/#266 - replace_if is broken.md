# #266 - [experimental/types] replace_if is broken [Closed]

> Username: ldionne  
> Created at: 2016-04-06 22:39:26 UTC  
> Updated at: 2016-05-28 23:10:07 UTC  
> Closed at: 2016-05-28 23:10:07 UTC  
> Url: https://github.com/boostorg/hana/issues/266  

The following code does not work:  
  
``` c++  
#include <boost/hana/bool.hpp>  
#include <boost/hana/experimental/types.hpp>  
#include <boost/hana/replace_if.hpp>  
#include <boost/hana/type.hpp>  
namespace hana = boost::hana;  
  
struct predicate { hana::true_ operator()(...) const; };  
  
int main() {  
    auto types = hana::experimental::types<void, void>{};  
    hana::replace_if(types, predicate{}, hana::type<struct anything>{});  
}  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2016-04-06 22:40:45 UTC  
> Url: https://github.com/boostorg/hana/issues/266#issuecomment-206599509  

The compiler error is  
  
```  
[snip]/boost/hana/transform.hpp:40:27: error: no matching member function for call to 'apply'  
        return Transform::apply(static_cast<Xs&&>(xs), static_cast<F&&>(f));  
               ~~~~~~~~~~~^~~~~  
[snip]/boost/hana/adjust_if.hpp:72:20: note: in instantiation of function template specialization  
      'boost::hana::transform_t::operator()<boost::hana::experimental::types<void, void> &, boost::hana::detail::apply_if<predicate,  
      boost::hana::_always<boost::hana::type_impl<anything>::_> > >' requested here  
            return hana::transform(static_cast<Xs&&>(xs),  
                   ^  
[snip]/boost/hana/adjust_if.hpp:37:26: note: in instantiation of function template specialization  
      'boost::hana::adjust_if_impl<boost::hana::experimental::types_tag, boost::hana::when<true> >::apply<boost::hana::experimental::types<void, void> &, predicate,  
      boost::hana::_always<boost::hana::type_impl<anything>::_> >' requested here  
        return AdjustIf::apply(static_cast<Xs&&>(xs), pred, f);  
                         ^  
[snip]/boost/hana/replace_if.hpp:46:20: note: in instantiation of function template specialization  
      'boost::hana::adjust_if_t::operator()<boost::hana::experimental::types<void, void> &, predicate, boost::hana::_always<boost::hana::type_impl<anything>::_> >' requested here  
            return hana::adjust_if(static_cast<Xs&&>(xs),  
                   ^  
[snip]/boost/hana/replace_if.hpp:36:27: note: in instantiation of function template specialization  
      'boost::hana::replace_if_impl<boost::hana::experimental::types_tag, boost::hana::when<true> >::apply<boost::hana::experimental::types<void, void> &, predicate,  
      boost::hana::type_impl<anything>::_>' requested here  
        return ReplaceIf::apply(static_cast<Xs&&>(xs),  
                          ^  
[snip]/hana/test/worksheet.cpp:1324:21: note: in instantiation of function template specialization  
      'boost::hana::replace_if_t::operator()<boost::hana::experimental::types<void, void> &, predicate, boost::hana::type_impl<anything>::_>' requested here  
    hana::replace_if(types, predicate{}, hana::type<struct anything>{});  
                    ^  
[snip]/boost/hana/experimental/types.hpp:80:31: note: candidate template ignored: substitution failure [with T = <void, void>, F =  
      boost::hana::detail::apply_if<predicate, boost::hana::_always<boost::hana::type_impl<anything>::_> >, $2 = void]: type 'decltype(f(hana::type<void>()))'  
      (aka 'const boost::hana::type_impl<anything>::_ &') cannot be used prior to '::' because it has no members  
        static constexpr auto apply(hana::experimental::types<T...> const&, F&& f)  
                              ^  
[snip]/boost/hana/experimental/types.hpp:85:13: note: candidate template ignored: disabled by 'enable_if' [with T = <void, void>, F =  
      boost::hana::detail::apply_if<predicate, boost::hana::_always<boost::hana::type_impl<anything>::_> >]  
            hana::Metafunction<F>::value  
            ^  
1 error generated.  
```
