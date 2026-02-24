# #204 - [mpl] Add support for converting an mpl::list to a hana::tuple [Closed]

> Username: samkellett  
> Created at: 2015-11-12 20:33:59 UTC  
> Updated at: 2015-11-12 20:37:08 UTC  
> Closed at: 2015-11-12 20:36:23 UTC  
> Url: https://github.com/boostorg/hana/issues/204  

i may be being a fool, but i'm not sure that commit e13d826496192af4c4228210a2b6661174c9a55a worked...  
  
my test case:  
  
``` c++  
#include <boost/hana.hpp>  
#include <boost/variant.hpp>  
  
namespace hana = boost::hana;  
  
int main()  
{  
  using my_variant = boost::variant<int, bool, std::string>;  
  auto my_tuple(hana::to<hana::tuple_tag>(my_variant::types{}));  
  
  static_assert(my_tuple == hana::tuple_t<int, bool, std::string>);  
}  
  
```  
  
fails on el capitan, xcode 7.1 with the following error:  
  
```  
samkellett@Sams-Air:~/devel/patch$ g++ -std=c++1z -Ihana/include -I/usr/local/include test.cpp  
In file included from test.cpp:1:  
In file included from hana/include/boost/hana.hpp:53:  
In file included from hana/include/boost/hana/adapt_adt.hpp:15:  
In file included from hana/include/boost/hana/detail/struct_macros.hpp:27:  
In file included from hana/include/boost/hana/string.hpp:15:  
In file included from hana/include/boost/hana/bool.hpp:16:  
hana/include/boost/hana/core/convert.hpp:49:13: error: static_assert failed "no conversion is available between the provided types"  
            static_assert(detail::wrong<to_impl<To, From>, X>{},  
            ^             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
hana/include/boost/hana/core/convert.hpp:75:45: note: in instantiation of function template specialization 'boost::hana::to_impl<boost::hana::tuple_tag, boost::mpl::l_item<mpl_::long_<3>, int, boost::mpl::l_item<mpl_::long_<2>, bool, boost::mpl::l_item<mpl_::long_<1>, std::__1::basic_string<char>, boost::mpl::l_end> > >, boost::hana::when<true> >::apply<boost::mpl::l_item<mpl_::long_<3>, int, boost::mpl::l_item<mpl_::long_<2>, bool, boost::mpl::l_item<mpl_::long_<1>, std::__1::basic_string<char>, boost::mpl::l_end> > > >' requested here  
                                            apply(static_cast<X&&>(x));  
                                            ^  
test.cpp:10:42: note: in instantiation of function template specialization 'boost::hana::to_t<boost::hana::tuple_tag>::operator()<boost::mpl::l_item<mpl_::long_<3>, int, boost::mpl::l_item<mpl_::long_<2>, bool, boost::mpl::l_item<mpl_::long_<1>, std::__1::basic_string<char>, boost::mpl::l_end> > > >' requested here  
  auto my_tuple(hana::to<hana::tuple_tag>(my_variant::types{}));  
                                         ^  
test.cpp:10:8: error: variable has incomplete type 'void'  
  auto my_tuple(hana::to<hana::tuple_tag>(my_variant::types{}));  
       ^  
2 errors generated.  
samkellett@Sams-Air:~/devel/patch$  
```  
  
the commit is definitely there:  
  
```  
samkellett@Sams-Air:~/devel/patch$ (cd hana/ && git log -2 )  
commit e77801512e806858ff062c8b057bea6ce39c7651  
Author: Louis Dionne <ldionne.2@gmail.com>  
Date:   Thu Nov 12 14:30:44 2015 -0500  
  
    [Doc] Fix the name of BOOST_MPL_MAX_XXX_SIZE, and add links  
  
commit e13d826496192af4c4228210a2b6661174c9a55a  
Author: Louis Dionne <ldionne.2@gmail.com>  
Date:   Thu Nov 12 14:23:00 2015 -0500  
  
    [MPL] Add support for mpl::list  
samkellett@Sams-Air:~/devel/patch$  
```

---

## Comment 1

> Username: samkellett  
> Created at: 2015-11-12 20:36:23 UTC  
> Url: https://github.com/boostorg/hana/issues/204#issuecomment-156226514  

oh god i am being a fool.. no header!!  
  
```  
#include <boost/hana/ext/boost/mpl/list.hpp>  
```  
  
... :(

---

## Comment 2

> Username: ldionne  
> Created at: 2015-11-12 20:36:40 UTC  
> Url: https://github.com/boostorg/hana/issues/204#issuecomment-156226585  

You're missing the `<boost/hana/ext/boost/mpl/list.hpp>` header. As explained in the documentation (section [header organization](http://boostorg.github.io/hana/index.html#tutorial-header_organization)), external dependencies are not included by `<boost/hana.hpp>`, because they're just optional features and not everybody wants to pay for them.

---

## Comment 3

> Username: samkellett  
> Created at: 2015-11-12 20:36:59 UTC  
> Url: https://github.com/boostorg/hana/issues/204#issuecomment-156226666  

yep. sorry for the noise

---

## Comment 4

> Username: ldionne  
> Created at: 2015-11-12 20:37:08 UTC  
> Url: https://github.com/boostorg/hana/issues/204#issuecomment-156226715  

No problem!
