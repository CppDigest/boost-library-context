# #194 - GCC suggest-override warnings in archive [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 18:48:04 UTC  
> Updated at: 2022-08-16 21:40:33 UTC  
> Closed at: 2022-08-16 21:40:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/194  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted::  
  
<pre>  
./boost/archive/archive_exception.hpp:92:45: warning: ‘virtual const char* boost::archive::archive_exception::what() const’ can be marked override [-Wsuggest-override]  
./boost/archive/codecvt_null.hpp:55:18: warning: ‘virtual bool boost::archive::codecvt_null<char>::do_always_noconv() const’ can be marked override [-Wsuggest-override]  
./boost/archive/codecvt_null.hpp:70:5: warning: ‘virtual std::codecvt_base::result boost::archive::codecvt_null<wchar_t>::do_out(mbstate_t&, const wchar_t*, const wchar_t*, const wchar_t*&, char*, char*, char*&) const’ can be marked override [-Wsuggest-override]  
./boost/archive/codecvt_null.hpp:80:5: warning: ‘virtual std::codecvt_base::result boost::archive::codecvt_null<wchar_t>::do_in(mbstate_t&, const char*, const char*, const char*&, wchar_t*, wchar_t*, wchar_t*&) const’ can be marked override [-Wsuggest-override]  
./boost/archive/codecvt_null.hpp:89:17: warning: ‘virtual int boost::archive::codecvt_null<wchar_t>::do_encoding() const’ can be marked override [-Wsuggest-override]  
./boost/archive/codecvt_null.hpp:92:17: warning: ‘virtual int boost::archive::codecvt_null<wchar_t>::do_max_length() const’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_iarchive.hpp:45:18: warning: ‘void boost::archive::detail::common_iarchive<Archive>::vload(boost::archive::version_type&) [with Archive = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_iarchive.hpp:48:18: warning: ‘void boost::archive::detail::common_iarchive<Archive>::vload(boost::archive::object_id_type&) [with Archive = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_iarchive.hpp:51:18: warning: ‘void boost::archive::detail::common_iarchive<Archive>::vload(boost::archive::class_id_type&) [with Archive = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_iarchive.hpp:54:18: warning: ‘void boost::archive::detail::common_iarchive<Archive>::vload(boost::archive::class_id_optional_type&) [with Archive = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_iarchive.hpp:57:18: warning: ‘void boost::archive::detail::common_iarchive<Archive>::vload(boost::archive::tracking_type&) [with Archive = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_iarchive.hpp:60:18: warning: ‘void boost::archive::detail::common_iarchive<Archive>::vload(boost::archive::class_name_type&) [with Archive = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_oarchive.hpp:43:18: warning: ‘void boost::archive::detail::common_oarchive<Archive>::vsave(boost::archive::version_type) [with Archive = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_oarchive.hpp:46:18: warning: ‘void boost::archive::detail::common_oarchive<Archive>::vsave(boost::archive::object_id_type) [with Archive = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_oarchive.hpp:49:18: warning: ‘void boost::archive::detail::common_oarchive<Archive>::vsave(boost::archive::object_reference_type) [with Archive = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_oarchive.hpp:52:18: warning: ‘void boost::archive::detail::common_oarchive<Archive>::vsave(boost::archive::class_id_type) [with Archive = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_oarchive.hpp:55:18: warning: ‘void boost::archive::detail::common_oarchive<Archive>::vsave(boost::archive::class_id_reference_type) [with Archive = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_oarchive.hpp:58:18: warning: ‘void boost::archive::detail::common_oarchive<Archive>::vsave(boost::archive::class_id_optional_type) [with Archive = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_oarchive.hpp:61:18: warning: ‘void boost::archive::detail::common_oarchive<Archive>::vsave(const boost::archive::class_name_type&) [with Archive = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/common_oarchive.hpp:64:18: warning: ‘void boost::archive::detail::common_oarchive<Archive>::vsave(boost::archive::tracking_type) [with Archive = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:102:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(signed char&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:105:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(unsigned char&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:110:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(wchar_t&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:115:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(short int&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:118:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(short unsigned int&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:121:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(int&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:124:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(unsigned int&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:127:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(long int&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:130:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(long unsigned int&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:134:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(boost::long_long_type&) [with ArchiveImplementation = boost::archive::binary_iarchive; boost::long_long_type = long long int]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:137:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(boost::ulong_long_type&) [with ArchiveImplementation = boost::archive::binary_iarchive; boost::ulong_long_type = long long unsigned int]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:148:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(float&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:151:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(double&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:154:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(std::__cxx11::string&) [with ArchiveImplementation = boost::archive::binary_iarchive; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:158:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(std::__cxx11::wstring&) [with ArchiveImplementation = boost::archive::binary_iarchive; std::__cxx11::wstring = std::__cxx11::basic_string<wchar_t>]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:158:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(std::__cxx11::wstring&) [with ArchiveImplementation = boost::archive::text_iarchive; std::__cxx11::wstring = std::__cxx11::basic_string<wchar_t>]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:163:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load_start(const char*) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:166:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load_end(const char*) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:169:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::register_basic_serializer(const boost::archive::detail::basic_iserializer&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:173:5: warning: ‘boost::archive::detail::helper_collection& boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::get_helper_collection() [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:59:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load_object(void*, const boost::archive::detail::basic_iserializer&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:65:47: warning: ‘const boost::archive::detail::basic_pointer_iserializer* boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load_pointer(void*&, const boost::archive::detail::basic_pointer_iserializer*, const boost::archive::detail::basic_pointer_iserializer* (*)(const boost::serialization::extended_type_info&)) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:74:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::set_library_version(boost::archive::library_version_type) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:77:34: warning: ‘boost::archive::library_version_type boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::get_library_version() const [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:80:26: warning: ‘unsigned int boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::get_flags() const [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:83:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::delete_created_pointers() [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:86:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::reset_object_address(const void*, const void*) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:92:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load_binary(void*, std::size_t) [with ArchiveImplementation = boost::archive::binary_iarchive; std::size_t = long unsigned int]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:96:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(bool&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_iarchive_route.hpp:99:18: warning: ‘void boost::archive::detail::polymorphic_iarchive_route<ArchiveImplementation>::load(char&) [with ArchiveImplementation = boost::archive::binary_iarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:100:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(int) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:103:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(unsigned int) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:106:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(long int) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:109:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(long unsigned int) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:113:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(boost::long_long_type) [with ArchiveImplementation = boost::archive::binary_oarchive; boost::long_long_type = long long int]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:116:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(boost::ulong_long_type) [with ArchiveImplementation = boost::archive::binary_oarchive; boost::ulong_long_type = long long unsigned int]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:127:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(float) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:130:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(double) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:133:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(const string&) [with ArchiveImplementation = boost::archive::binary_oarchive; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:137:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(const wstring&) [with ArchiveImplementation = boost::archive::binary_oarchive; std::__cxx11::wstring = std::__cxx11::basic_string<wchar_t>]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:141:34: warning: ‘boost::archive::library_version_type boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::get_library_version() const [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:144:26: warning: ‘unsigned int boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::get_flags() const [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:147:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save_binary(const void*, std::size_t) [with ArchiveImplementation = boost::archive::binary_oarchive; std::size_t = long unsigned int]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:151:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save_start(const char*) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:154:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save_end(const char*) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:157:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::end_preamble() [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:160:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::register_basic_serializer(const boost::archive::detail::basic_oserializer&) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:164:5: warning: ‘boost::archive::detail::helper_collection& boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::get_helper_collection() [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:59:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save_object(const void*, const boost::archive::detail::basic_oserializer&) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:65:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save_pointer(const void*, const boost::archive::detail::basic_pointer_oserializer*) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:71:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save_null_pointer() [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:75:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(bool) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:78:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(char) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:81:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(signed char) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:81:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(signed char) [with ArchiveImplementation = boost::archive::text_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:81:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(signed char) [with ArchiveImplementation = boost::archive::text_woarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:81:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(signed char) [with ArchiveImplementation = boost::archive::xml_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:81:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(signed char) [with ArchiveImplementation = boost::archive::xml_woarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:84:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(unsigned char) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:89:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(wchar_t) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:94:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(short int) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/detail/polymorphic_oarchive_route.hpp:97:18: warning: ‘void boost::archive::detail::polymorphic_oarchive_route<ArchiveImplementation>::save(short unsigned int) [with ArchiveImplementation = boost::archive::binary_oarchive]’ can be marked override [-Wsuggest-override]  
./boost/archive/iterators/dataflow_exception.hpp:49:25: warning: ‘virtual const char* boost::archive::iterators::dataflow_exception::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-04-28 20:38:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/194#issuecomment-620841645  

Patch: [archive.txt](https://github.com/boostorg/serialization/files/4548340/archive.txt)  
  
Also fix some misspellings in comments, Clang -Wextra-semi and Clang-tidy modernize-redundant-void-arg warnings.

---

## Comment 2

> Username: robertramey  
> Created at: 2020-04-29 01:26:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/194#issuecomment-620939384  

This would be better formulated as a merge request.    
a) Fork the repo,  
b) make the changes.    
c) Run them on your local machine.   
d) prepare the merge request.  
e) watch the CI results.  
Let me know when it passes.  I'll look at it and see if I can merge it in.

---

## Comment 3

> Username: EugeneZelenko  
> Created at: 2020-05-04 04:55:56 UTC  
> Updated at: 2020-05-04 04:56:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/194#issuecomment-623256660  

Please take a look at #200.

---

## Comment 4

> Username: robertramey  
> Created at: 2020-05-04 14:10:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/194#issuecomment-623487511  

So we're at step e)  
  
Very good.  I will take a look at this in the next couple of days.  Thanks for your help and efforts.

---

## Comment 5

> Username: EugeneZelenko  
> Created at: 2020-05-04 14:16:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/194#issuecomment-623491031  

Tests that were run is OK, but I'm not sure that this is complete set, because there were more tests run on other merge request.

---

## Comment 6

> Username: robertramey  
> Created at: 2022-08-16 21:40:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/194#issuecomment-1217195898  

looks like this hasn't created any other issues.  So I'm closing it.
