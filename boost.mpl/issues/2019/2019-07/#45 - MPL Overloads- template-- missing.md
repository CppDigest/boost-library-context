# #45 - MPL Overloads: template<> missing [Open]

> Username: ax3l  
> Created at: 2019-07-29 17:21:57 UTC  
> Updated at: 2023-02-08 14:29:52 UTC  
> Url: https://github.com/boostorg/mpl/issues/45  

Compiling boost MPL with (NVCC 10.1.168 and) GCC 6.4.0 and `-Wall -Wextra` throws the following warnings. Mainly `-Wnon-template-friend` and `operatorXY ... declares a non-template function -- add <> to refer to a template instance`  
  
Generally, the macros for overloads seem to lack `template <>` and similar annotations:  
  
```  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(54): warning: "boost::mpl::aux::type_wrapper<T> operator/(const boost::mpl::m_item<Key, T, Base> &, boost::mpl::aux::type_wrapper<Key> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(55): warning: "boost::mpl::aux::type_wrapper<boost::mpl::pair<Key, T>> operator|(const boost::mpl::m_item<Key, T, Base> &, boost::mpl::next<Base::order>::type *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(56): warning: "char (&operator||(const boost::mpl::m_item<Key, T, Base> &, boost::mpl::aux::type_wrapper<Key> *))[boost::mpl::next<Base::order>::type::value]" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(71): warning: "boost::mpl::aux::type_wrapper<mpl_::void_> operator/(const boost::mpl::m_mask<Key, Base> &, boost::mpl::aux::type_wrapper<Key> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(72): warning: "boost::mpl::aux::type_wrapper<mpl_::void_> operator|(const boost::mpl::m_mask<Key, Base> &, boost::mpl::x_order_impl<Base, Key>::type *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp(47): warning: "char (&operator||(const boost::mpl::s_item<T, Base> &, boost::mpl::aux::type_wrapper<T> *))[boost::mpl::next<Base::order>::type::value]" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp(48): warning: "boost::mpl::aux::no_tag operator%(const boost::mpl::s_item<T, Base> &, boost::mpl::aux::type_wrapper<T> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp(63): warning: "boost::mpl::aux::yes_tag operator%(const boost::mpl::s_mask<T, Base> &, boost::mpl::aux::type_wrapper<T> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp(77): warning: "boost::mpl::aux::no_tag operator%(const boost::mpl::s_unmask<T, Base> &, boost::mpl::aux::type_wrapper<T> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(54): warning: "boost::mpl::aux::type_wrapper<T> operator/(const boost::mpl::m_item<Key, T, Base> &, boost::mpl::aux::type_wrapper<Key> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(55): warning: "boost::mpl::aux::type_wrapper<boost::mpl::pair<Key, T>> operator|(const boost::mpl::m_item<Key, T, Base> &, boost::mpl::next<Base::order>::type *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(56): warning: "char (&operator||(const boost::mpl::m_item<Key, T, Base> &, boost::mpl::aux::type_wrapper<Key> *))[boost::mpl::next<Base::order>::type::value]" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(71): warning: "boost::mpl::aux::type_wrapper<mpl_::void_> operator/(const boost::mpl::m_mask<Key, Base> &, boost::mpl::aux::type_wrapper<Key> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp(72): warning: "boost::mpl::aux::type_wrapper<mpl_::void_> operator|(const boost::mpl::m_mask<Key, Base> &, boost::mpl::x_order_impl<Base, Key>::type *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp(47): warning: "char (&operator||(const boost::mpl::s_item<T, Base> &, boost::mpl::aux::type_wrapper<T> *))[boost::mpl::next<Base::order>::type::value]" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp(48): warning: "boost::mpl::aux::no_tag operator%(const boost::mpl::s_item<T, Base> &, boost::mpl::aux::type_wrapper<T> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp(63): warning: "boost::mpl::aux::yes_tag operator%(const boost::mpl::s_mask<T, Base> &, boost::mpl::aux::type_wrapper<T> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp(77): warning: "boost::mpl::aux::no_tag operator%(const boost::mpl::s_unmask<T, Base> &, boost::mpl::aux::type_wrapper<T> *)" declares a non-template function -- add <> to refer to a template instance  
  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp: At global scope:  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp:54:83: warning: friend declaration 'boost::mpl::aux::type_wrapper<Key> boost::mpl::operator/(const boost::mpl::m_item<Key, T, Base>&, boost::mpl::aux::type_wrapper<T>*)' declares a non-template function [-Wnon-template-friend]  
     BOOST_MPL_AUX_MAP_OVERLOAD( aux::type_wrapper<T>, VALUE_BY_KEY, m_item, aux::type_wrapper<Key>* );  
                                                                                   ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp:54:83: note: (if this is not what you intended, make sure the function template has already been declared and add <> after the function name here)   
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp:55:77: warning: friend declaration 'boost::mpl::aux::type_wrapper<boost::mpl::pair<T1, T2> > boost::mpl::operator|(const boost::mpl::m_item<Key, T, Base>&, boost::mpl::m_item<Key, T, Base>::order*)' declares a non-template function [-Wnon-template-friend]  
     BOOST_MPL_AUX_MAP_OVERLOAD( aux::type_wrapper<item>, ITEM_BY_ORDER, m_item, order* );  
                                                                             ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp:56:72: warning: friend declaration 'char (& boost::mpl::operator||(const boost::mpl::m_item<Key, T, Base>&, boost::mpl::aux::type_wrapper<T>*))[boost::mpl::m_item<Key, T, Base>::order:: value]' declares a non-template function [-Wnon-template-friend]  
     BOOST_MPL_AUX_MAP_OVERLOAD( order_tag_, ORDER_BY_KEY, m_item, aux::type_wrapper<Key>* );  
                                                                        ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp:71:93: warning: friend declaration 'boost::mpl::aux::type_wrapper<mpl_::void_> boost::mpl::operator/(const boost::mpl::m_mask<Key, Base>&, boost::mpl::aux::type_wrapper<T>*)' declares a non-template function [-Wnon-template-friend]  
     BOOST_MPL_AUX_MAP_OVERLOAD( aux::type_wrapper<void_>, VALUE_BY_KEY, m_mask, aux::type_wrapper<Key>* );  
                                                                                             ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/map/aux_/item.hpp:72:79: warning: friend declaration 'boost::mpl::aux::type_wrapper<mpl_::void_> boost::mpl::operator|(const boost::mpl::m_mask<Key, Base>&, boost::mpl::m_mask<Key, Base>::key_order_*)' declares a non-template function [-Wnon-template-friend]  
     BOOST_MPL_AUX_MAP_OVERLOAD( aux::type_wrapper<void_>, ITEM_BY_ORDER, m_mask, key_order_* );  
                                                                               ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp:47:70: warning: friend declaration 'char (& boost::mpl::operator||(const boost::mpl::s_item<T, Base>&, boost::mpl::aux::type_wrapper<T>*))[boost::mpl::s_item<T, Base>::order:: value]' declares a non-template function [-Wnon-template-friend]  
     BOOST_MPL_AUX_SET_OVERLOAD( order_tag_, ORDER_BY_KEY, s_item, aux::type_wrapper<T>* );  
                                                                      ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp:48:70: warning: friend declaration 'char (& boost::mpl::operator%(const boost::mpl::s_item<T, Base>&, boost::mpl::aux::type_wrapper<T>*))[1]' declares a non-template function [-Wnon-template-friend]  
     BOOST_MPL_AUX_SET_OVERLOAD( aux::no_tag, IS_MASKED, s_item, aux::type_wrapper<T>* );  
                                                                      ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp:63:71: warning: friend declaration 'char (& boost::mpl::operator%(const boost::mpl::s_mask<T, Base>&, boost::mpl::aux::type_wrapper<T>*))[2]' declares a non-template function [-Wnon-template-friend]  
     BOOST_MPL_AUX_SET_OVERLOAD( aux::yes_tag, IS_MASKED, s_mask, aux::type_wrapper<T>* );  
                                                                       ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/mpl/set/aux_/item.hpp:77:72: warning: friend declaration 'char (& boost::mpl::operator%(const boost::mpl::s_unmask<T, Base>&, boost::mpl::aux::type_wrapper<T>*))[1]' declares a non-template function [-Wnon-template-friend]  
     BOOST_MPL_AUX_SET_OVERLOAD( aux::no_tag, IS_MASKED, s_unmask, aux::type_wrapper<T>* );  
```  
  
Seen with [PIConGPU](https://github.com/ComputationalRadiationPhysics/picongpu/) `0.5.0-dev` as of https://github.com/ComputationalRadiationPhysics/picongpu/commit/1347ebb46d378d93bdf8f9c0ab7ceee0c9a2142a (and way before as well).  
  
Small example with NVCC 10.1 that triggers this via the boost property tree:  
https://cuda.godbolt.org/z/ir3p6i  
```C++  
#include <boost/property_tree/ptree.hpp>  
  
int main()  
{  
  using boost::property_tree::ptree;  
  ptree pt;  
}  
```

---

## Comment 1

> Username: ax3l  
> Created at: 2019-07-29 22:15:17 UTC  
> Url: https://github.com/boostorg/mpl/issues/45#issuecomment-516182915  

The same warnings exist in boost 1.70.0

---

## Comment 2

> Username: jeking3  
> Created at: 2022-05-01 21:26:17 UTC  
> Url: https://github.com/boostorg/mpl/issues/45#issuecomment-1114340772  

Hi, does https://github.com/boostorg/mpl/pull/62 fix this?

---

## Comment 3

> Username: masterleinad  
> Created at: 2023-02-08 14:25:14 UTC  
> Url: https://github.com/boostorg/mpl/issues/45#issuecomment-1422680334  

> Hi, does #62 fix this?  
  
Doesn't look like it.

---

## Comment 4

> Username: masterleinad  
> Created at: 2023-02-08 14:29:52 UTC  
> Url: https://github.com/boostorg/mpl/issues/45#issuecomment-1422687634  

https://cuda.godbolt.org/z/s73P9a553 demonstrates that this warning still occurs with `boost-1.81.0`.
