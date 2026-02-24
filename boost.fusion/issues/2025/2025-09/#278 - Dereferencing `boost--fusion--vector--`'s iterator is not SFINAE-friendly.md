# #278 - Dereferencing `boost::fusion::vector<>`'s iterator is not SFINAE-friendly [Open]

> Username: yaito3014  
> Created at: 2025-09-11 13:25:35 UTC  
> Updated at: 2025-09-11 13:31:37 UTC  
> Url: https://github.com/boostorg/fusion/issues/278  

Consider following code:  
```cpp  
#include <boost/fusion/container/vector.hpp>  
  
#include <type_traits>  
  
template <class Iterator, class = void>  
struct is_dereferenceable : std::false_type {};  
  
template <class Iterator>  
struct is_dereferenceable<Iterator, decltype(*boost::declval<Iterator>())> : std::true_type {};  
  
using empty_vector = boost::fusion::vector<>;  
using empty_vector_iterator = boost::fusion::result_of::begin<empty_vector>::type;  
static_assert(!is_dereferenceable<empty_vector_iterator>::value);  
  
int main() {}  
```  
  
This code should compile successfully, guaranteeing `empty_vector_iterator` is never dereferenceable.  
However, this emits following hard errors:  
```  
In file included from /usr/include/boost/fusion/container/vector.hpp:10,  
                 from source.cpp:1:  
/usr/include/boost/fusion/container/vector/detail/value_at_impl.hpp: In instantiation of ‘struct boost::fusion::extension::value_at_impl<boost::fusion::vector_tag>::apply<boost::fusion::vector<>, mpl_::int_<0> >’:  
/usr/include/boost/fusion/container/vector/detail/deref_impl.hpp:33:97:   required from ‘struct boost::fusion::extension::deref_impl<boost::fusion::vector_iterator_tag>::apply<boost::fusion::vector_iterator<boost::fusion::vector<>, 0> >’  
/usr/include/boost/fusion/iterator/deref.hpp:51:16:   required from ‘struct boost::fusion::result_of::deref<boost::fusion::vector_iterator<boost::fusion::vector<>, 0> >’  
/usr/include/boost/fusion/iterator/deref.hpp:69:5:   required by substitution of ‘template<class Iterator> constexpr typename boost::fusion::result_of::deref<Iterator>::type boost::fusion::operator*(const iterator_base<Iterator>&) [with Iterator = boost::fusion::vector_iterator<boost::fusion::vector<>, 0>]’  
source.cpp:9:46:   required by substitution of ‘template<class Iterator> struct is_dereferenceable<Iterator, decltype (* declval<Iterator>())> [with Iterator = boost::fusion::vector_iterator<boost::fusion::vector<>, 0>]’  
source.cpp:13:57:   required from here  
/usr/include/boost/fusion/container/vector/detail/value_at_impl.hpp:50:28: error: no matching function for call to ‘value_at_impl<mpl_::int_<0>::value>(boost::fusion::vector<>*)’  
   50 |             struct apply : BOOST_FUSION_DECLTYPE_N3031((  
      |                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/boost/fusion/container/vector/detail/at_impl.hpp:12,  
                 from /usr/include/boost/fusion/container/vector/vector.hpp:29,  
                 from /usr/include/boost/fusion/container/vector.hpp:12:  
/usr/include/boost/fusion/container/vector/detail/value_at_impl.hpp:38:26: note: candidate: ‘template<long unsigned int N, class U> boost::mpl::identity<U> boost::fusion::vector_detail::value_at_impl(const volatile store<N, U>*)’  
   38 |         mpl::identity<U> value_at_impl(store<N, U> const volatile*);  
      |                          ^~~~~~~~~~~~~  
/usr/include/boost/fusion/container/vector/detail/value_at_impl.hpp:38:26: note:   template argument deduction/substitution failed:  
/usr/include/boost/fusion/container/vector/detail/value_at_impl.hpp:50:28: note:   ‘boost::fusion::vector<>’ is not derived from ‘const volatile boost::fusion::vector_detail::store<0, U>’  
   50 |             struct apply : BOOST_FUSION_DECLTYPE_N3031((  
      |                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/boost/fusion/container/vector/vector_iterator.hpp:12,  
                 from /usr/include/boost/fusion/container/vector/detail/begin_impl.hpp:11,  
                 from /usr/include/boost/fusion/container/vector/vector.hpp:31:  
/usr/include/boost/fusion/container/vector/detail/deref_impl.hpp: In instantiation of ‘struct boost::fusion::extension::deref_impl<boost::fusion::vector_iterator_tag>::apply<boost::fusion::vector_iterator<boost::fusion::vector<>, 0> >’:  
/usr/include/boost/fusion/iterator/deref.hpp:51:16:   required from ‘struct boost::fusion::result_of::deref<boost::fusion::vector_iterator<boost::fusion::vector<>, 0> >’  
/usr/include/boost/fusion/iterator/deref.hpp:69:5:   required by substitution of ‘template<class Iterator> constexpr typename boost::fusion::result_of::deref<Iterator>::type boost::fusion::operator*(const iterator_base<Iterator>&) [with Iterator = boost::fusion::vector_iterator<boost::fusion::vector<>, 0>]’  
source.cpp:9:46:   required by substitution of ‘template<class Iterator> struct is_dereferenceable<Iterator, decltype (* declval<Iterator>())> [with Iterator = boost::fusion::vector_iterator<boost::fusion::vector<>, 0>]’  
source.cpp:13:57:   required from here  
/usr/include/boost/fusion/container/vector/detail/deref_impl.hpp:33:97: error: no type named ‘type’ in ‘struct boost::fusion::extension::value_at_impl<boost::fusion::vector_tag>::apply<boost::fusion::vector<>, mpl_::int_<0> >’  
   33 |                 typedef typename value_at_impl<vector_tag>::template apply<vector, index>::type element;  
      |                                                                                                 ^~~~~~~  
/usr/include/boost/fusion/container/vector/detail/deref_impl.hpp:41:17: error: no type named ‘type’ in ‘struct boost::fusion::extension::value_at_impl<boost::fusion::vector_tag>::apply<boost::fusion::vector<>, mpl_::int_<0> >’  
   41 |                 type;  
      |                 ^~~~  
  
```  
  
This breaks some existing constrained codes.

---

## Comment 1

> Username: saki7  
> Created at: 2025-09-11 13:31:37 UTC  
> Url: https://github.com/boostorg/fusion/issues/278#issuecomment-3280708528  

Confirmed during pair programming. This raises false-positive hard error on Spirit.X3's constraints, which ultimately leads to hard error on Spirit.X3's test case:  
  
https://github.com/boostorg/spirit/blob/e1973fcb314dfd051e6c30e7f1f8278adb1caf9f/test/x3/omit.cpp#L66-L67  
  
The constraint is currently worked around so the error is not showing up on X3's CI.  
  
https://github.com/boostorg/spirit/blob/e1973fcb314dfd051e6c30e7f1f8278adb1caf9f/include/boost/spirit/home/x3/support/traits/container_traits.hpp#L31
