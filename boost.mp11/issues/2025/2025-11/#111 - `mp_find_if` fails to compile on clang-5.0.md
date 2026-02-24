# #111 - `mp_find_if` fails to compile on clang-5.0 [Closed]

> Username: Lastique  
> Created at: 2025-11-06 01:37:03 UTC  
> Updated at: 2025-11-06 02:27:25 UTC  
> Closed at: 2025-11-06 02:27:25 UTC  
> Url: https://github.com/boostorg/mp11/issues/111  

This has appeared in Boost.Log CI recently:  
  
```  
clang-linux.compile.c++ bin.v2/libs/log/build/clang-linux-5/debug/x86_64/cxxstd-1z-iso/threading-multi/visibility-hidden/setup/init_from_stream.o  
In file included from /__w/log/log/boost-root/libs/log/src/setup/init_from_stream.cpp:19:  
In file included from ./boost/log/utility/setup/from_settings.hpp:25:  
In file included from ./boost/log/utility/setup/settings.hpp:26:  
In file included from ./boost/property_tree/ptree.hpp:21:  
In file included from ./boost/multi_index_container.hpp:25:  
./boost/mp11/algorithm.hpp:816:29: error: non-type template argument is not a constant expression  
    using type = mp_size_t< cx_find_index( _v, _v + sizeof...(T) ) >;  
                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/mp11/algorithm.hpp:853:47: note: in instantiation of template class 'boost::mp11::detail::mp_find_if_impl<boost::mp11::mp_list<boost::multi_index::detail::sequenced_index<boost::multi_index::detail::nth_layer<1, std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, boost::multi_index::indexed_by<boost::multi_index::sequenced<boost::multi_index::tag<> >, boost::multi_index::ordered_non_unique<boost::multi_index::tag<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::subs::by_name>, boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, &std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >::first>, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, std::allocator<std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > > >, boost::multi_index::tag<> >, boost::multi_index::detail::ordered_index<boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, &std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >::first>, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::multi_index::detail::nth_layer<2, std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, boost::multi_index::indexed_by<boost::multi_index::sequenced<boost::multi_index::tag<> >, boost::multi_index::ordered_non_unique<boost::multi_index::tag<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::subs::by_name>, boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, &std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >::first>, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, std::allocator<std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > > >, boost::multi_index::tag<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::subs::by_name>, boost::multi_index::detail::ordered_non_unique_tag, boost::multi_index::detail::null_augment_policy> >, fn>' requested here  
template<class L, template<class...> class P> using mp_find_if = typename detail::mp_find_if_impl<L, P>::type;  
                                              ^  
./boost/mp11/algorithm.hpp:854:28: note: in instantiation of template type alias 'mp_find_if' requested here  
template<class L, class Q> using mp_find_if_q = mp_find_if<L, Q::template fn>;  
                           ^  
./boost/multi_index_container.hpp:446:19: note: in instantiation of template type alias 'mp_find_if_q' requested here  
    typedef mp11::mp_find_if_q<  
                  ^  
./boost/property_tree/detail/ptree_implementation.hpp:59:42: note: in instantiation of template class 'boost::multi_index::multi_index_container<std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, boost::multi_index::indexed_by<boost::multi_index::sequenced<boost::multi_index::tag<> >, boost::multi_index::ordered_non_unique<boost::multi_index::tag<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::subs::by_name>, boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, &std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >::first>, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, std::allocator<std::pair<const std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > > >::index<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::subs::by_name>' requested here  
        typedef typename base_container::template index<by_name>::type  
                                         ^  
./boost/property_tree/detail/ptree_implementation.hpp:210:17: note: in instantiation of member class 'boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::subs' requested here  
        delete &subs::ch(this);  
                ^  
./boost/log/utility/setup/settings.hpp:603:9: note: in instantiation of member function 'boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::~basic_ptree' requested here  
        delete this->m_ptree;  
        ^  
/__w/log/log/boost-root/libs/log/src/setup/init_from_stream.cpp:31:24: note: in instantiation of member function 'boost::log::v2_mt_posix::basic_settings<char>::~basic_settings' requested here  
    init_from_settings(parse_settings(strm));  
                       ^  
```  
  
[Full log](https://github.com/boostorg/log/actions/runs/19117847394/job/54631341200#step:7:2389).  
  
This happens with clang-5.0 in C++-17 mode, as `mp_find_if` is invoked by Boost.MultiIndex as it is used by Boost.PropertyTree.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-11-06 01:51:38 UTC  
> Url: https://github.com/boostorg/mp11/issues/111#issuecomment-3494442172  

Yes, I know. I no longer support clang 5 in -std=c++1z mode. I don't think anybody apart from our CI uses this configuration.  
  
https://github.com/boostorg/mp11/commit/43fd186a8eb5d2b3dcc04cde49092b5be2bc4085

---

## Comment 2

> Username: Lastique  
> Created at: 2025-11-06 02:08:08 UTC  
> Url: https://github.com/boostorg/mp11/issues/111#issuecomment-3494528832  

I see, thanks. Have you considered using the C++14 branch on this compiler?  
  
In any case, feel free to close this if you don't plan to do anything about this.

---

## Comment 3

> Username: pdimov  
> Created at: 2025-11-06 02:27:25 UTC  
> Url: https://github.com/boostorg/mp11/issues/111#issuecomment-3494574415  

This is the C++14 branch. The C++17 branch has other problems.
