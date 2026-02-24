# #61 fix warning in mb_from_wchar [Closed]

> Username: jeking3  
> Created at: 2017-09-09 20:01:33 UTC  
> Updated at: 2017-11-08 17:38:23 UTC  
> Closed at: 2017-11-08 17:38:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/61  

Original warning:  
```  
In file included from ../../../boost/archive/impl/xml_oarchive_impl.ipp:28:0,  
                 from ../../../libs/serialization/src/xml_oarchive.cpp:23:  
../../../boost/archive/iterators/mb_from_wchar.hpp: In instantiation of â€˜void boost::archive::iterators::mb_from_wchar<Base>::fill() [with Base = boost::archive::iterators::xml_escape<const wchar_t*>]â€™:  
../../../boost/archive/iterators/mb_from_wchar.hpp:65:17:   required from â€˜char boost::archive::iterators::mb_from_wchar<Base>::dereference_impl() [with Base = boost::archive::iterators::xml_escape<const wchar_t*>]â€™  
../../../boost/archive/iterators/mb_from_wchar.hpp:72:63:   required from â€˜char boost::archive::iterators::mb_from_wchar<Base>::dereference() const [with Base = boost::archive::iterators::xml_escape<const wchar_t*>]â€™  
../../../boost/iterator/iterator_facade.hpp:549:32:   required from â€˜static typename Facade::reference boost::iterators::iterator_core_access::dereference(const Facade&) [with Facade = boost::archive::iterators::mb_from_wchar<boost::archive::iterators::xml_escape<const wchar_t*> >; typename Facade::reference = char]â€™  
../../../boost/iterator/iterator_facade.hpp:655:53:   required from â€˜boost::iterators::detail::iterator_facade_base<Derived, Value, CategoryOrTraversal, Reference, Difference, false, false>::reference boost::iterators::detail::iterator_facade_base<Derived, Value, CategoryOrTraversal, Reference, Difference, false, false>::operator*() const [with Derived = boost::archive::iterators::mb_from_wchar<boost::archive::iterators::xml_escape<const wchar_t*> >; Value = wchar_t; CategoryOrTraversal = boost::iterators::single_pass_traversal_tag; Reference = char; Difference = long int; boost::iterators::detail::iterator_facade_base<Derived, Value, CategoryOrTraversal, Reference, Difference, false, false>::reference = char]â€™  
/usr/include/c++/6/bits/stl_algobase.h:294:18:   required from â€˜static _OI std::__copy_move<<anonymous>, <anonymous>, <template-parameter-1-3> >::__copy_m(_II, _II, _OI) [with _II = boost::archive::iterators::mb_from_wchar<boost::archive::iterators::xml_escape<const wchar_t*> >; _OI = boost::archive::iterators::ostream_iterator<char>; bool <anonymous> = false; bool <anonymous> = false; <template-parameter-1-3> = std::input_iterator_tag]â€™  
/usr/include/c++/6/bits/stl_algobase.h:386:44:   required from â€˜_OI std::__copy_move_a(_II, _II, _OI) [with bool _IsMove = false; _II = boost::archive::iterators::mb_from_wchar<boost::archive::iterators::xml_escape<const wchar_t*> >; _OI = boost::archive::iterators::ostream_iterator<char>]â€™  
/usr/include/c++/6/bits/stl_algobase.h:422:45:   required from â€˜_OI std::__copy_move_a2(_II, _II, _OI) [with bool _IsMove = false; _II = boost::archive::iterators::mb_from_wchar<boost::archive::iterators::xml_escape<const wchar_t*> >; _OI = boost::archive::iterators::ostream_iterator<char>]â€™  
/usr/include/c++/6/bits/stl_algobase.h:455:8:   required from â€˜_OI std::copy(_II, _II, _OI) [with _II = boost::archive::iterators::mb_from_wchar<boost::archive::iterators::xml_escape<const wchar_t*> >; _OI = boost::archive::iterators::ostream_iterator<char>]â€™  
../../../boost/archive/impl/xml_oarchive_impl.ipp:45:14:   required from â€˜void boost::archive::save_iterator(std::ostream&, InputIterator, InputIterator) [with InputIterator = const wchar_t*; std::ostream = std::basic_ostream<char>]â€™  
../../../boost/archive/impl/xml_oarchive_impl.ipp:67:18:   required from â€˜void boost::archive::xml_oarchive_impl<Archive>::save(const wchar_t*) [with Archive = boost::archive::xml_oarchive]â€™  
../../../libs/serialization/src/xml_oarchive.cpp:30:16:   required from here  
../../../boost/archive/iterators/mb_from_wchar.hpp:89:35: warning: unused variable â€˜râ€™ [-Wunused-variable]  
         std::codecvt_base::result r = m_codecvt_facet.out(  
                                   ^  
```

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-11-08 17:38:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/61#issuecomment-342895360  

fixed

---
