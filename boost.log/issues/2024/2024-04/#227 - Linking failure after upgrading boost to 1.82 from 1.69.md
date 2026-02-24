# #227 - Linking failure after upgrading boost to 1.82 from 1.69 [Closed]

> Username: sagunkho  
> Created at: 2024-04-03 06:37:56 UTC  
> Updated at: 2024-04-03 08:30:51 UTC  
> Closed at: 2024-04-03 08:30:51 UTC  
> Url: https://github.com/boostorg/log/issues/227  

I had boost 1.69 which was working fine, I have upgraded the version to 1.82 and not changed any of the logger setup or configuration.   
The logger fails to link with the executable now.   
  
I'm using boost components - log and log_setup  
both configure correctly and are used in `target_link_libraries`. The linked libraries are -  
```  
C:/vcpkg/installed/x64-windows/lib/boost_locale-vc140-mt.lib  
debugC:/vcpkg/installed/x64-windows/debug/lib/boost_locale-vc140-mt-gd.lib  
optimizedC:/vcpkg/installed/x64-windows/lib/boost_filesystem-vc140-mt.lib  
debugC:/vcpkg/installed/x64-windows/debug/lib/boost_filesystem-vc140-mt-gd.lib  
optimizedC:/vcpkg/installed/x64-windows/lib/boost_log-vc140-mt.lib  
debugC:/vcpkg/installed/x64-windows/debug/lib/boost_log-vc140-mt-gd.lib  
optimizedC:/vcpkg/installed/x64-windows/lib/boost_log_setup-vc140-mt.lib  
debugC:/vcpkg/installed/x64-windows/debug/lib/boost_log_setup-vc140-mt-gd.lib  
optimizedC:/vcpkg/installed/x64-windows/lib/boost_unit_test_framework-vc140-mt.lib  
debugC:/vcpkg/installed/x64-windows/debug/lib/boost_unit_test_framework-vc140-mt-gd.lib  
optimizedC:/vcpkg/installed/x64-windows/lib/boost_thread-vc140-mt.lib  
debugC:/vcpkg/installed/x64-windows/debug/lib/boost_thread-vc140-mt-gd.lib  
optimizedC:/vcpkg/installed/x64-windows/lib/boost_regex-vc140-mt.lib  
debugC:/vcpkg/installed/x64-windows/debug/lib/boost_regex-vc140-mt-gd.lib  
optimizedC:/vcpkg/installed/x64-windows/lib/boost_chrono-vc140-mt.lib  
debugC:/vcpkg/installed/x64-windows/debug/lib/boost_chrono-vc140-mt-gd.lib  
optimizedC:/vcpkg/installed/x64-windows/lib/boost_atomic-vc140-mt.lib  
debugC:/vcpkg/installed/x64-windows/debug/lib/boost_atomic-vc140-mt-gd.lib  
```  
  
The linking fails -  
```  
[build] LoggingTest.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) private: static unsigned int __cdecl boost::log::v2_mt_nt6::attribute_name::get_id_from_string(char const *)" (__imp_?get_id_from_string@attribute_name@v2_mt_nt6@log@boost@@CAIPEBD@Z) referenced in function "void __cdecl init(void)" (?init@@YAXXZ) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] Logger.obj : error LNK2001: unresolved external symbol "__declspec(dllimport) private: static unsigned int __cdecl boost::log::v2_mt_nt6::attribute_name::get_id_from_string(char const *)" (__imp_?get_id_from_string@attribute_name@v2_mt_nt6@log@boost@@CAIPEBD@Z) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] LoggingTest.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: static void * __cdecl boost::log::v2_mt_nt6::attribute::impl::operator new(unsigned __int64)" (__imp_??2impl@attribute@v2_mt_nt6@log@boost@@SAPEAX_K@Z) referenced in function "void __cdecl boost::log::v2_mt_nt6::add_common_attributes(void)" (?add_common_attributes@v2_mt_nt6@log@boost@@YAXXZ) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] Logger.obj : error LNK2001: unresolved external symbol "__declspec(dllimport) public: static void * __cdecl boost::log::v2_mt_nt6::attribute::impl::operator new(unsigned __int64)" (__imp_??2impl@attribute@v2_mt_nt6@log@boost@@SAPEAX_K@Z) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] LoggingTest.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: static void __cdecl boost::log::v2_mt_nt6::attribute::impl::operator delete(void *,unsigned __int64)" (__imp_??3impl@attribute@v2_mt_nt6@log@boost@@SAXPEAX_K@Z) referenced in function "public: virtual void * __cdecl boost::log::v2_mt_nt6::attributes::attribute_value_impl<unsigned int>::`scalar deleting destructor'(unsigned int)" (??_G?$attribute_value_impl@I@attributes@v2_mt_nt6@log@boost@@UEAAPEAXI@Z) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] Logger.obj : error LNK2001: unresolved external symbol "__declspec(dllimport) public: static void __cdecl boost::log::v2_mt_nt6::attribute::impl::operator delete(void *,unsigned __int64)" (__imp_??3impl@attribute@v2_mt_nt6@log@boost@@SAXPEAX_K@Z) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] LoggingTest.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: __cdecl boost::log::v2_mt_nt6::attribute_set::attribute_set(void)" (__imp_??0attribute_set@v2_mt_nt6@log@boost@@QEAA@XZ) referenced in function "public: void __cdecl LoggingTest::test_method(void)" (?test_method@LoggingTest@@QEAAXXZ) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] LoggingTest.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: __cdecl boost::log::v2_mt_nt6::attribute_set::~attribute_set(void)" (__imp_??1attribute_set@v2_mt_nt6@log@boost@@QEAA@XZ) referenced in function "public: __cdecl boost::log::v2_mt_nt6::sources::basic_logger<char,class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level>,struct boost::log::v2_mt_nt6::sources::single_thread_model>::~basic_logger<char,class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level>,struct boost::log::v2_mt_nt6::sources::single_thread_model>(void)" (??1?$basic_logger@DV?$severity_logger@W4severity_level@trivial@v2_mt_nt6@log@boost@@@sources@v2_mt_nt6@log@boost@@Usingle_thread_model@2345@@sources@v2_mt_nt6@log@boost@@QEAA@XZ) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] LoggingTest.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: struct std::pair<class boost::log::v2_mt_nt6::attribute_set::iter<0>,bool> __cdecl boost::log::v2_mt_nt6::attribute_set::insert(class boost::log::v2_mt_nt6::attribute_name,class boost::log::v2_mt_nt6::attribute const &)" (__imp_?insert@attribute_set@v2_mt_nt6@log@boost@@QEAA?AU?$pair@V?$iter@$0A@@attribute_set@v2_mt_nt6@log@boost@@_N@std@@Vattribute_name@234@AEBVattribute@234@@Z) referenced in function "public: void __cdecl LoggingTest::test_method(void)" (?test_method@LoggingTest@@QEAAXXZ) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] LoggingTest.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: class boost::log::v2_mt_nt6::attribute_value_set::const_iterator __cdecl boost::log::v2_mt_nt6::attribute_value_set::end(void)const " (__imp_?end@attribute_value_set@v2_mt_nt6@log@boost@@QEBA?AVconst_iterator@1234@XZ) referenced in function "public: class boost::log::v2_mt_nt6::visitation_result __cdecl boost::log::v2_mt_nt6::value_visitor_invoker<struct boost::mpl::vector2<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > >,struct boost::log::v2_mt_nt6::fallback_to_none>::operator()<struct boost::log::v2_mt_nt6::binder1st<struct boost::log::v2_mt_nt6::output_fun,class boost::log::v2_mt_nt6::expressions::aux::stream_ref<class boost::log::v2_mt_nt6::basic_formatting_ostream<char,struct std::char_traits<char>,class std::allocator<char> > > &> >(class boost::log::v2_mt_nt6::attribute_name const &,class boost::log::v2_mt_nt6::attribute_value_set const &,struct boost::log::v2_mt_nt6::binder1st<struct boost::log::v2_mt_nt6::output_fun,class boost::log::v2_mt_nt6::expressions::aux::stream_ref<class boost::log::v2_mt_nt6::basic_formatting_ostream<char,struct std::char_traits<char>,class std::allocator<char> > > &>)const " (??$?RU?$binder1st@Uoutput_fun@v2_mt_nt6@log@boost@@AEAV?$stream_ref@V?$basic_formatting_ostream@DU?$char_traits@D@std@@V?$allocator@D@2@@v2_mt_nt6@log@boost@@@aux@expressions@234@@v2_mt_nt6@log@boost@@@?$value_visitor_invoker@U?$vector2@V?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@V?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@2@@mpl@boost@@Ufallback_to_none@v2_mt_nt6@log@3@@v2_mt_nt6@log@boost@@QEBA?AVvisitation_result@123@AEBVattribute_name@123@AEBVattribute_value_set@123@U?$binder1st@Uoutput_fun@v2_mt_nt6@log@boost@@AEAV?$stream_ref@V?$basic_formatting_ostream@DU?$char_traits@D@std@@V?$allocator@D@2@@v2_mt_nt6@log@boost@@@aux@expressions@234@@123@@Z) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] Logger.obj : error LNK2001: unresolved external symbol "__declspec(dllimport) public: class boost::log::v2_mt_nt6::attribute_value_set::const_iterator __cdecl boost::log::v2_mt_nt6::attribute_value_set::end(void)const " (__imp_?end@attribute_value_set@v2_mt_nt6@log@boost@@QEBA?AVconst_iterator@1234@XZ) [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
[build] LoggingTest.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: class boost::log::v2_mt_nt6::attribute_value_set::const_iterator __cdecl boost::log::v2_mt_nt6::attribute_value_set::find(class boost::log::v2_mt_nt6::attribute_name)const " (__imp_?find@attribute_value_set@v2_mt_nt6@log@boost@@QEBA?AVconst_iterator@1234@Vattribute_name@234@@Z) referenced in function "public: class boost::log::v2_mt_nt6::visitation_result __cdecl boost::log::v2_mt_nt6::value_visitor_invoker<struct boost::mpl::vector2<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > >,struct boost::log::v2_mt_nt6::fallback_to_none>::operator()<struct boost::log::v2_mt_nt6::binder1st<struct boost::log::v2_mt_nt6::output_fun,class boost::log::v2_mt_nt6::expressions::aux::stream_ref<class boost::log::v2_mt_nt6::basic_formatting_ostream<char,struct std::char_traits<char>,class std::allocator<char> > > &> >(class boost::log::v2_mt_nt6::attribute_name const &,class boost::log::v2_mt_nt6::attribute_value_set const &,struct boost::log::v2_mt_nt6::binder1st<struct boost::log::v2_mt_nt6::output_fun,class boost::log::v2_mt_nt6::expressions::aux::stream_ref<class boost::log::v2_mt_nt6::basic_formatting_ostream<char,struct std::char_traits<char>,class std::allocator<char> > > &>)const " (??$?RU?$binder1st@Uoutput_fun@v2_mt_nt6@log@boost@@AEAV?$stream_ref@V?$basic_formatting_ostream@DU?$char_traits@D@std@@V?  
[build] C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\install\Horizon-Tests\test\Release\LoggingTest.exe : fatal error LNK1120: 56 unresolved externals [C:\Users\Sagun Khosla\Desktop\Repos\horizon\out\build\Horizon-Tests\src\Tests\LoggingTest.vcxproj]  
```  
  
I'm using vcpkg to install boost-log

---

## Comment 1

> Username: Lastique  
> Created at: 2024-04-03 08:07:27 UTC  
> Url: https://github.com/boostorg/log/issues/227#issuecomment-2033847474  

Check [this page](https://www.boost.org/doc/libs/1_82_0/libs/log/doc/html/log/rationale/namespace_mangling.html) in the docs.  
  
I suspect that Boost.Log was built for a different Windows version than your code. Most likely, Boost.Log was built for Windows 10 and you are building for an older version (Windows 7?). You can verify this by comparing the version namespace between the error messages and what the compiled library implements, specifically its trailing part (`nt6`).  
  
To fix this you need to make sure `_WIN32_WINNT` is defined to the same target [Windows version](https://msdn.microsoft.com/en-us/library/6sehtctf.aspx) when Boost and your code is compiled. Either rebuild Boost with the version you need or update the value defined for your code. Alternatively, you may define `BOOST_USE_WINAPI_VERSION` to the target Windows version (again, the same value when building Boost and your code). This macro controls the Windows version targeted by Boost. This will allow to define `_WIN32_WINNT` differently for Boost and your code (usually, to a value higher than `BOOST_USE_WINAPI_VERSION`).

---

## Comment 2

> Username: sagunkho  
> Created at: 2024-04-03 08:30:48 UTC  
> Url: https://github.com/boostorg/log/issues/227#issuecomment-2033906475  

Adding a definition for `_WIN32_WINNT` worked. Thanks @Lastique
