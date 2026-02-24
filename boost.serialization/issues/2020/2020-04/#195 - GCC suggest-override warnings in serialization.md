# #195 - GCC suggest-override warnings in serialization [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 18:52:56 UTC  
> Updated at: 2020-05-30 20:03:42 UTC  
> Closed at: 2020-05-30 20:03:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/195  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/serialization/extended_type_info_no_rtti.hpp:63:5: warning: ‘virtual bool boost::serialization::no_rtti_system::extended_type_info_no_rtti_0::is_less_than(const boost::serialization::extended_type_info&) const’ can be marked override [-Wsuggest-override]  
./boost/serialization/extended_type_info_no_rtti.hpp:65:5: warning: ‘virtual bool boost::serialization::no_rtti_system::extended_type_info_no_rtti_0::is_equal(const boost::serialization::extended_type_info&) const’ can be marked override [-Wsuggest-override]  
./boost/serialization/extended_type_info_typeid.hpp:55:26: warning: ‘virtual const char* boost::serialization::typeid_system::extended_type_info_typeid_0::get_debug_info() const’ can be marked override [-Wsuggest-override]  
./boost/serialization/extended_type_info_typeid.hpp:70:5: warning: ‘virtual bool boost::serialization::typeid_system::extended_type_info_typeid_0::is_less_than(const boost::serialization::extended_type_info&) const’ can be marked override [-Wsuggest-override]  
./boost/serialization/extended_type_info_typeid.hpp:72:5: warning: ‘virtual bool boost::serialization::typeid_system::extended_type_info_typeid_0::is_equal(const boost::serialization::extended_type_info&) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/basic_oarchive.cpp:182:14: warning: ‘virtual bool boost::archive::detail::basic_oarchive_impl::find(const boost::serialization::extended_type_info&) const::bosarg::class_info() const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/basic_oarchive.cpp:187:14: warning: ‘virtual bool boost::archive::detail::basic_oarchive_impl::find(const boost::serialization::extended_type_info&) const::bosarg::tracking(unsigned int) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/basic_oarchive.cpp:192:22: warning: ‘virtual boost::archive::version_type boost::archive::detail::basic_oarchive_impl::find(const boost::serialization::extended_type_info&) const::bosarg::version() const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/basic_oarchive.cpp:197:14: warning: ‘virtual bool boost::archive::detail::basic_oarchive_impl::find(const boost::serialization::extended_type_info&) const::bosarg::is_polymorphic() const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/basic_oarchive.cpp:201:14: warning: ‘virtual void boost::archive::detail::basic_oarchive_impl::find(const boost::serialization::extended_type_info&) const::bosarg::save_object_data(boost::archive::detail::basic_oarchive&, const void*) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/extended_type_info.cpp:83:5: warning: ‘virtual bool boost::serialization::detail::extended_type_info_arg::is_less_than(const boost::serialization::extended_type_info&) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/extended_type_info.cpp:88:5: warning: ‘virtual bool boost::serialization::detail::extended_type_info_arg::is_equal(const boost::serialization::extended_type_info&) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/extended_type_info.cpp:92:26: warning: ‘virtual const char* boost::serialization::detail::extended_type_info_arg::get_debug_info() const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/extended_type_info.cpp:95:20: warning: ‘virtual void* boost::serialization::detail::extended_type_info_arg::construct(unsigned int, ...) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/extended_type_info.cpp:99:18: warning: ‘virtual void boost::serialization::detail::extended_type_info_arg::destroy(const void*) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/extended_type_info_typeid.cpp:128:20: warning: ‘virtual void* boost::serialization::typeid_system::extended_type_info_typeid_arg::construct(unsigned int, ...) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/extended_type_info_typeid.cpp:132:18: warning: ‘virtual void boost::serialization::typeid_system::extended_type_info_typeid_arg::destroy(const void*) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/void_cast.cpp:105:18: warning: ‘virtual bool boost::serialization::void_cast_detail::void_caster_shortcut::has_virtual_base() const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/void_cast.cpp:191:5: warning: ‘virtual const void* boost::serialization::void_cast_detail::void_caster_argument::upcast(const void*) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/void_cast.cpp:196:5: warning: ‘virtual const void* boost::serialization::void_cast_detail::void_caster_argument::downcast(const void*) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/void_cast.cpp:200:18: warning: ‘virtual bool boost::serialization::void_cast_detail::void_caster_argument::has_virtual_base() const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/void_cast.cpp:91:5: warning: ‘virtual const void* boost::serialization::void_cast_detail::void_caster_shortcut::upcast(const void*) const’ can be marked override [-Wsuggest-override]  
libs/serialization/src/void_cast.cpp:97:5: warning: ‘virtual const void* boost::serialization::void_cast_detail::void_caster_shortcut::downcast(const void*) const’ can be marked override [-Wsuggest-override]  
</pre>  
  
Depends on boostorg/config#329.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-04-12 19:22:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/195#issuecomment-612663721  

I would not hold my breath waiting for a change here.  The overriding of the names save/load is pretty much essential to understanding the code and avoiding a proliferation of similar but different things which mean the same thing.  Best would be that that warning not be enabled.  
  
If anyone finds that is a big problem for their applications I suggest they consider invoking a #pragma to suppress the warnings before serialization headers are included.

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-04-12 19:30:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/195#issuecomment-612664745  

Why marking methods are overridden explicitly would make code less readable or understandable?

---

## Comment 3

> Username: EugeneZelenko  
> Created at: 2020-04-13 22:48:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/195#issuecomment-613134494  

`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 4

> Username: robertramey  
> Created at: 2020-04-13 23:11:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/195#issuecomment-613140926  

LOL - just 7 hours ago.  Looks like pretty good service.  If you want you might make the changes in your copy and post a PR which after short examination I'll merge in as it looks innocuous.  If you don't want to do that, it will just take more time.

---

## Comment 5

> Username: EugeneZelenko  
> Created at: 2020-04-13 23:13:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/195#issuecomment-613141517  

I'll try to do this next weekend, but if you could do this faster il'll be great.

---

## Comment 6

> Username: EugeneZelenko  
> Created at: 2020-05-30 20:03:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/195#issuecomment-636377656  

Fixed with #200.
