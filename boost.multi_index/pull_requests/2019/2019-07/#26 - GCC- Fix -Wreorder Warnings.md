# #26 GCC: Fix -Wreorder Warnings [Closed]

> Username: ax3l  
> Created at: 2019-07-29 14:39:24 UTC  
> Updated at: 2019-07-30 17:37:51 UTC  
> Closed at: 2019-07-30 17:32:59 UTC  
> Url: https://github.com/boostorg/multi_index/pull/26  

Fix `-Wreorder` warnings in the initialization of multi_index_container.  
  
Seen with GCC 6.4.0 .

---

## Comment 1

> Username: joaquintides  
> Created_at: 2019-07-30 10:00:10 UTC  
> Url: https://github.com/boostorg/multi_index/pull/26#issuecomment-516353017  

Hi Axel,  
  
This is weird, because the inheritance order of `multi_index_container` is  
1. `bfm_allocator`,  
2. `bfm_header`,  
3. `super`,  
  
that is, as currently initialized. [This example](https://wandbox.org/permlink/Dc6zC9sjOnPIjwED) does issue  a `-Wreorder` warning for `X` but none for `multi_index_container`.  Maybe you can provide me with some reproducible test case?

---

## Comment 2

> Username: ax3l  
> Created_at: 2019-07-30 16:45:13 UTC  
> Updated_at: 2019-07-30 16:55:44 UTC  
> Url: https://github.com/boostorg/multi_index/pull/26#issuecomment-516500382  

Hm, you are right. I was mismatching the order of `typedef`s with those.  
I saw this with [PIConGPU](https://github.com/ComputationalRadiationPhysics/picongpu/) `0.5.0-dev` as of https://github.com/ComputationalRadiationPhysics/picongpu/commit/1347ebb46d378d93bdf8f9c0ab7ceee0c9a2142a (and way before as well), (NVCC 10.1.168 and) GCC 6.4.0 and `-Wall -Wextra`. I'll have to see if I can reduce this issue further.  
  
The full warning is from [[1]](https://github.com/ComputationalRadiationPhysics/picongpu/blob/d776bf7ec29b974532e79ff16b53e62db354bdd6/include/picongpu/plugins/ResourceLog.hpp#L108-L109) is:  
```C++  
using boost::property_tree::ptree;  
ptree pt;  
```  
```  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/multi_index_container.hpp: In instantiation of 'boost::multi_index::multi_index_container<Value, IndexSpecifierList, Allocator>::multi_index_container(const ctor_args_list&, const allocator_type&) [with Value = std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >; IndexSpecifierList = boost::multi_index::indexed_by<boost::multi_index::sequenced<boost::multi_index::tag<> >, boost::multi_index::ordered_non_unique<boost::multi_index::tag<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >::subs::by_name, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, const std::__cxx11::basic_string<char>, &std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >::first>, std::less<std::__cxx11::basic_string<char> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>; Allocator = std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >; boost::multi_index::multi_index_container<Value, IndexSpecifierList, Allocator>::ctor_args_list = boost::tuples::cons<boost::tuples::null_type, boost::tuples::cons<boost::tuples::tuple<boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, const std::__cxx11::basic_string<char>, &std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >::first>, std::less<std::__cxx11::basic_string<char> >, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>, boost::tuples::null_type> >; boost::multi_index::multi_index_container<Value, IndexSpecifierList, Allocator>::allocator_type = std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >]':  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/property_tree/detail/ptree_implementation.hpp:179:52:   required from 'boost::property_tree::basic_ptree<Key, Data, KeyCompare>::basic_ptree() [with Key = std::__cxx11::basic_string<char>; Data = std::__cxx11::basic_string<char>; KeyCompare = std::less<std::__cxx11::basic_string<char> >]'  
/ccs/home/huebl/src/picongpu/include/pmacc/../picongpu/plugins/ResourceLog.hpp:109:29:   required from here  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/multi_index_container.hpp:175:100: warning: base 'boost::multi_index::detail::sequenced_index<boost::multi_index::detail::nth_layer<1, std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, boost::multi_index::indexed_by<boost::multi_index::sequenced<boost::multi_index::tag<> >, boost::multi_index::ordered_non_unique<boost::multi_index::tag<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >::subs::by_name, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, const std::__cxx11::basic_string<char>, &std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >::first>, std::less<std::__cxx11::basic_string<char> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > > >, boost::mpl::vector0<> >' will be initialized after [-Wreorder]  
     const allocator_type& al=allocator_type()):  
                                                                                                    ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/multi_index_container.hpp:175:100: warning:   base 'boost::base_from_member<std::allocator<boost::multi_index::detail::sequenced_index_node<boost::multi_index::detail::ordered_index_node<boost::multi_index::detail::null_augment_policy, boost::multi_index::detail::index_node_base<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > > > > > >, 0>' [-Wreorder]  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/multi_index_container.hpp:160:10: warning:   when initialized here [-Wreorder]  
   explicit multi_index_container(  
          ^~~~~~~~~~~~~~~~~~~~~  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/multi_index_container.hpp: In instantiation of 'boost::multi_index::multi_index_container<Value, IndexSpecifierList, Allocator>::multi_index_container(const boost::multi_index::multi_index_container<Value, IndexSpecifierList, Allocator>&) [with Value = std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >; IndexSpecifierList = boost::multi_index::indexed_by<boost::multi_index::sequenced<boost::multi_index::tag<> >, boost::multi_index::ordered_non_unique<boost::multi_index::tag<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >::subs::by_name, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, const std::__cxx11::basic_string<char>, &std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >::first>, std::less<std::__cxx11::basic_string<char> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>; Allocator = std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >]':  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/property_tree/detail/ptree_implementation.hpp:191:94:   required from 'boost::property_tree::basic_ptree<Key, Data, KeyCompare>::basic_ptree(const boost::property_tree::basic_ptree<Key, Data, KeyCompare>&) [with Key = std::__cxx11::basic_string<char>; Data = std::__cxx11::basic_string<char>; KeyCompare = std::less<std::__cxx11::basic_string<char> >]'  
/autofs/nccs-svm1_sw/summit/gcc/6.4.0/include/c++/6.4.0/bits/stl_pair.h:296:78:   required from 'constexpr std::pair<_T1, _T2>::pair(_U1&&, const _T2&) [with _U1 = std::__cxx11::basic_string<char>&; typename std::enable_if<std::_PCC<true, _T1, _T2>::_MoveCopyPair<true, _U1, _T2>(), bool>::type <anonymous> = 1u; _T1 = const std::__cxx11::basic_string<char>; _T2 = boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >]'  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/property_tree/detail/ptree_implementation.hpp:646:25:   required from 'boost::property_tree::basic_ptree<K, D, C>& boost::property_tree::basic_ptree<Key, Data, KeyCompare>::put_child(const path_type&, const self_type&) [with Key = std::__cxx11::basic_string<char>; Data = std::__cxx11::basic_string<char>; KeyCompare = std::less<std::__cxx11::basic_string<char> >; boost::property_tree::basic_ptree<Key, Data, KeyCompare>::path_type = boost::property_tree::string_path<std::__cxx11::basic_string<char>, boost::property_tree::id_translator<std::__cxx11::basic_string<char> > >; boost::property_tree::basic_ptree<Key, Data, KeyCompare>::self_type = boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >]'  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/property_tree/detail/ptree_implementation.hpp:848:30:   required from 'boost::property_tree::basic_ptree<K, D, C>& boost::property_tree::basic_ptree<Key, Data, KeyCompare>::put(const path_type&, const Type&, Translator) [with Type = long unsigned int; Translator = boost::property_tree::stream_translator<char, std::char_traits<char>, std::allocator<char>, long unsigned int>; Key = std::__cxx11::basic_string<char>; Data = std::__cxx11::basic_string<char>; KeyCompare = std::less<std::__cxx11::basic_string<char> >; boost::property_tree::basic_ptree<Key, Data, KeyCompare>::path_type = boost::property_tree::string_path<std::__cxx11::basic_string<char>, boost::property_tree::id_translator<std::__cxx11::basic_string<char> > >]'  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/property_tree/detail/ptree_implementation.hpp:859:71:   required from 'boost::property_tree::basic_ptree<K, D, C>& boost::property_tree::basic_ptree<Key, Data, KeyCompare>::put(const path_type&, const Type&) [with Type = long unsigned int; Key = std::__cxx11::basic_string<char>; Data = std::__cxx11::basic_string<char>; KeyCompare = std::less<std::__cxx11::basic_string<char> >; boost::property_tree::basic_ptree<Key, Data, KeyCompare>::path_type = boost::property_tree::string_path<std::__cxx11::basic_string<char>, boost::property_tree::id_translator<std::__cxx11::basic_string<char> > >]'  
/ccs/home/huebl/src/picongpu/include/pmacc/../picongpu/plugins/ResourceLog.hpp:114:32:   required from here  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/multi_index_container.hpp:262:86: warning: base 'boost::multi_index::detail::sequenced_index<boost::multi_index::detail::nth_layer<1, std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, boost::multi_index::indexed_by<boost::multi_index::sequenced<boost::multi_index::tag<> >, boost::multi_index::ordered_non_unique<boost::multi_index::tag<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >::subs::by_name, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, const std::__cxx11::basic_string<char>, &std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >::first>, std::less<std::__cxx11::basic_string<char> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > > >, boost::mpl::vector0<> >' will be initialized after [-Wreorder]  
     const multi_index_container<Value,IndexSpecifierList,Allocator>& x):  
                                                                                      ^  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/multi_index_container.hpp:262:86: warning:   base 'boost::base_from_member<std::allocator<boost::multi_index::detail::sequenced_index_node<boost::multi_index::detail::ordered_index_node<boost::multi_index::detail::null_augment_policy, boost::multi_index::detail::index_node_base<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > > > > > >, 0>' [-Wreorder]  
linux-rhel7-ppc64le/gcc-6.4.0/boost-1.66.0-nxj2e2htxvm25phwnxtwbw3bajgcryko/include/boost/multi_index_container.hpp:261:1: warning:   when initialized here [-Wreorder]  
   multi_index_container(  
 ^ ~~~~~~~~~~~~~~~~~~~  
```  
  
I seam not to be able to reproduce this in a smaller example such as  
```C++  
#include <boost/property_tree/ptree.hpp>  
  
  
int main()  
{  
  using boost::property_tree::ptree;  
  ptree pt;  
}  
```

---

## Comment 3

> Username: ax3l  
> Created_at: 2019-07-30 17:03:53 UTC  
> Updated_at: 2019-07-30 17:37:51 UTC  
> Url: https://github.com/boostorg/multi_index/pull/26#issuecomment-516507246  

The problem might be NVCC specific: https://cuda.godbolt.org/z/ir3p6i (updated)  
  
But even here I can only isolate https://github.com/boostorg/mpl/issues/45 and cannot yet trigger a warning in `multi_index_container` as in the original code.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2019-07-30 17:16:25 UTC  
> Url: https://github.com/boostorg/multi_index/pull/26#issuecomment-516511585  

Pardon me, what's the Godbolt snippet supposed to prove?

---

## Comment 5

> Username: ax3l  
> Created_at: 2019-07-30 17:32:59 UTC  
> Url: https://github.com/boostorg/multi_index/pull/26#issuecomment-516517501  

That's the problem. I reduced the compile to the source lines where this warning is triggered, but cannot trigger the exact same warning (yet, another MPL warning arises in parallel).  
Either way, this PR is definitely invalid.

---
