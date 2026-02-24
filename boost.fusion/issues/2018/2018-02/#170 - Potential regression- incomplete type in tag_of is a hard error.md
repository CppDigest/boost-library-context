# #170 - Potential regression: incomplete type in tag_of is a hard error [Closed]

> Username: ldionne  
> Created at: 2018-02-27 19:10:26 UTC  
> Updated at: 2018-03-03 04:31:23 UTC  
> Closed at: 2018-03-03 04:31:23 UTC  
> Url: https://github.com/boostorg/fusion/issues/170  

This issue was initially reported as https://github.com/boostorg/hana/issues/386. The following is a hard error in Boost trunk:  
  
```c++  
#include <boost/fusion/include/tag_of.hpp>  
struct incomplete;  
using X = ::boost::fusion::traits::tag_of<incomplete>::type;  
  
int main() { }  
```  
  
I believe this started happening with https://github.com/boostorg/type_traits/commit/ac351390b215b73d8a9e16d2ca37ba2d9d88a14f. `fusion::tag_of` is using `boost::is_convertible`, which requires its input to be a complete type, but I don't think it is documented anywhere that tags must be complete types. Possible workarounds are  
1. Only call `is_convertible` if `boost::is_complete` is true  
2. Request that tags be complete types (but that's a breaking change)  
3. Reimplement the `is_convertible` without the assertion  
  
I think (1) is the best solution, even though I am skeptical that a `is_complete` trait can even be implemented without compiler support. But since Boost.TypeTraits provides it, it seems like it's possible.  
  
  
The full error message (with Clang) is:  
  
```  
In file included from [snip].cpp:5:  
In file included from [snip]/boost/fusion/include/tag_of.hpp:11:  
In file included from [snip]/boost/fusion/support/tag_of.hpp:14:  
In file included from [snip]/boost/fusion/support/detail/is_mpl_sequence.hpp:16:  
[snip]/boost/type_traits/is_convertible.hpp:498:4: error: static_assert failed "From argument type to is_convertible must be a complete type"  
   BOOST_STATIC_ASSERT_MSG(boost::is_complete<From>::value || boost::is_void<From>::value || boost::is_array<From>::value, "From argument type to is_convertible must be a complete type");  
   ^                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[snip]/boost/static_assert.hpp:31:45: note: expanded from macro 'BOOST_STATIC_ASSERT_MSG'  
#     define BOOST_STATIC_ASSERT_MSG( ... ) static_assert(__VA_ARGS__)  
                                            ^             ~~~~~~~~~~~  
[snip]/boost/mpl/aux_/nested_type_wknd.hpp:27:7: note: in instantiation of template class 'boost::is_convertible<incomplete, boost::fusion::detail::from_sequence_convertible_type>' requested here  
    : T::type  
      ^  
[snip]/boost/mpl/not.hpp:41:11: note: in instantiation of template class 'boost::mpl::aux::nested_type_wknd<boost::is_convertible<incomplete, boost::fusion::detail::from_sequence_convertible_type> >' requested here  
          BOOST_MPL_AUX_NESTED_TYPE_WKND(T)::value  
          ^  
[snip]/boost/mpl/aux_/nested_type_wknd.hpp:38:24: note: expanded from macro 'BOOST_MPL_AUX_NESTED_TYPE_WKND'  
    ::boost::mpl::aux::nested_type_wknd<T> \  
                       ^  
[snip]/boost/mpl/aux_/nested_type_wknd.hpp:27:7: note: in instantiation of template class 'boost::mpl::not_<boost::is_convertible<incomplete, boost::fusion::detail::from_sequence_convertible_type> >' requested here  
    : T::type  
      ^  
[snip]/boost/mpl/aux_/preprocessed/gcc/and.hpp:51:11: note: in instantiation of template class 'boost::mpl::aux::nested_type_wknd<boost::mpl::not_<boost::is_convertible<incomplete, boost::fusion::detail::from_sequence_convertible_type> > >' requested here  
          BOOST_MPL_AUX_NESTED_TYPE_WKND(T1)::value  
          ^  
[snip]/boost/mpl/aux_/nested_type_wknd.hpp:38:24: note: expanded from macro 'BOOST_MPL_AUX_NESTED_TYPE_WKND'  
    ::boost::mpl::aux::nested_type_wknd<T> \  
                       ^  
[snip]/boost/fusion/support/detail/is_mpl_sequence.hpp:22:11: note: in instantiation of template class 'boost::mpl::and_<boost::mpl::not_<boost::is_convertible<incomplete, boost::fusion::detail::from_sequence_convertible_type> >, boost::mpl::is_sequence<incomplete>, mpl_::bool_<true>, mpl_::bool_<true>, mpl_::bool_<true> >' requested here  
        : mpl::and_<  
          ^  
[snip]/boost/mpl/if.hpp:63:68: note: in instantiation of template class 'boost::fusion::detail::is_mpl_sequence<incomplete>' requested here  
          BOOST_MPL_AUX_STATIC_CAST(bool, BOOST_MPL_AUX_VALUE_WKND(T1)::value)  
                                                                   ^  
[snip]/boost/fusion/support/tag_of.hpp:53:18: note: in instantiation of template class 'boost::mpl::if_<boost::fusion::detail::is_mpl_sequence<incomplete>, boost::mpl::identity<boost::fusion::mpl_sequence_tag>, boost::mpl::identity<boost::fusion::non_fusion_tag> >' requested here  
          : mpl::if_<fusion::detail::is_mpl_sequence<Sequence>,  
                 ^  
[snip]/boost/fusion/support/tag_of.hpp:71:15: note: in instantiation of template class 'boost::fusion::detail::tag_of_impl<incomplete, void>' requested here  
            : boost::fusion::detail::tag_of_impl<Sequence, Active>  
              ^  
[snip].cpp:7:36: note: in instantiation of template class 'boost::fusion::traits::tag_of<incomplete, void>' requested here  
using X = ::boost::fusion::traits::tag_of<incomplete>::type;  
                                   ^  
```

---

## Comment 1

> Username: Flast  
> Created at: 2018-03-01 08:27:44 UTC  
> Updated at: 2018-03-01 10:01:36 UTC  
> Url: https://github.com/boostorg/fusion/issues/170#issuecomment-369513528  

Is hana tag a sequence? It seems hana passes tag type to `tag_of`.  
  
> ../../../boost/fusion/support/tag_of.hpp:70:16:   required from ‘struct boost::fusion::traits::tag_of<boost::hana::ext::boost::fusion::deque_tag, void>’ (from boostorg/hana#386)

---

## Comment 2

> Username: ldionne  
> Created at: 2018-03-01 17:28:34 UTC  
> Updated at: 2018-03-01 17:29:53 UTC  
> Url: https://github.com/boostorg/fusion/issues/170#issuecomment-369666684  

This is expected. `hana::tag_of` is idempotent, so `hana::tag_of<boost::hana::ext::boost::fusion::deque_tag>::type` should be `boost::hana::ext::boost::fusion::deque_tag`. More precisely, here is what happens:  
  
```c++  
hana::any_of(fusion::make_deque(...), pred)  
using Tag = hana::tag_of<decltype(fusion::make_deque(...))>::type;  
if constexpr (hana::Searchable<Tag>::value) {  
  // do something  
}  
```  
  
But then, `hana::Searchable<Tag>` is really `hana::Searchable<boost::hana::ext::boost::fusion::deque_tag>`, which does (with `Tag = boost::hana::ext::boost::fusion::deque_tag`:  
  
```c++  
hana::integral_constant<bool,  
  !is_default<any_of_impl<hana::tag_of<Tag>::type>>::value &&  
  !is_default<find_if_impl<tag_of<Tag>::type>>::value  
>  
```  
  
Here, `tag_of<Tag>::type` is expected to return just `Tag` if it's already a tag, but it hard-errors when the fusion adapters are included and the tag is incomplete, because we probe for whether the Tag is a fusion tag in that case.  
  
Regardless of Hana's adapters, other clients of Fusion may have been using incomplete types for tags since this wasn't documented anywhere.

---

## Comment 3

> Username: djowel  
> Created at: 2018-03-01 21:44:14 UTC  
> Url: https://github.com/boostorg/fusion/issues/170#issuecomment-369741219  

By design, we should be able to use incomplete types.

---

## Comment 4

> Username: Flast  
> Created at: 2018-03-02 09:06:42 UTC  
> Url: https://github.com/boostorg/fusion/issues/170#issuecomment-369863192  

OK. Additionally, the change also should be applied to `fusion::is_sequence`.
