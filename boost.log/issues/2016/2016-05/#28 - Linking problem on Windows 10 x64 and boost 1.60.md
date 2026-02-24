# #28 - Linking problem on Windows 10 x64 and boost 1.60 [Closed]

> Username: Cynede  
> Created at: 2016-05-16 14:28:17 UTC  
> Updated at: 2016-05-17 09:50:33 UTC  
> Closed at: 2016-05-16 17:09:08 UTC  
> Url: https://github.com/boostorg/log/issues/28  

error has came after switching to boost `1.60`  
maybe related `On Windows, the configuration macro BOOST_LOG_USE_WINNT6_API is no longer used by the library. The target Windows version should be configured by defining macro BOOST_USE_WINAPI_VERSION`  
it seems like it's using `v2_mt_nt6`  
  
I'm using `BOOST_LOG_DYN_LINK`  
  
But it's still not clear for me what is using that and how can I change it  
  
I'm using `BOOST_LOG_STREAM_SEV` and `boost::log::sources::severity_logger< boost::log::trivial::severity_level > lg;`  
  
full log:  
  
```  
Error   4   error LNK2019: unresolved external symbol "__declspec(dllimport) private: void __cdecl boost::log::v2_mt_nt6::core::push_record_move(class boost::log::v2_mt_nt6::record &)" (__imp_?push_record_move@core@v2_mt_nt6@log@boost@@AEAAXAEAVrecord@234@@Z) referenced in function "public: void __cdecl boost::log::v2_mt_nt6::core::push_record(class boost::log::v2_mt_nt6::record &&)" (?push_record@core@v2_mt_nt6@log@boost@@QEAAX$$QEAVrecord@234@@Z)    D:\D\SB\MITK-superbuild\MITK-build\Modules\SceneSerialization\mitkSceneIO.obj  
Error   2   error LNK2019: unresolved external symbol "__declspec(dllimport) public: bool __cdecl boost::log::v2_mt_nt6::core::get_logging_enabled(void)const " (__imp_?get_logging_enabled@core@v2_mt_nt6@log@boost@@QEBA_NXZ) referenced in function "public: class boost::log::v2_mt_nt6::record __cdecl boost::log::v2_mt_nt6::sources::basic_composite_logger<char,class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level>,struct boost::log::v2_mt_nt6::sources::single_thread_model,struct boost::log::v2_mt_nt6::sources::features<struct boost::log::v2_mt_nt6::sources::severity<enum boost::log::v2_mt_nt6::trivial::severity_level> > >::open_record<struct boost::parameter::aux::tagged_argument<struct boost::log::v2_mt_nt6::keywords::tag::severity,enum boost::log::v2_mt_nt6::trivial::severity_level const > >(struct boost::parameter::aux::tagged_argument<struct boost::log::v2_mt_nt6::keywords::tag::severity,enum boost::log::v2_mt_nt6::trivial::severity_level const > const &)" (??$open_record@U?$tagged_argument@Useverity@tag@keywords@v2_mt_nt6@log@boost@@$$CBW4severity_level@trivial@456@@aux@parameter@boost@@@?$basic_composite_logger@DV?$severity_logger@W4severity_level@trivial@v2_mt_nt6@log@boost@@@sources@v2_mt_nt6@log@boost@@Usingle_thread_model@2345@U?$features@U?$severity@W4severity_level@trivial@v2_mt_nt6@log@boost@@@sources@v2_mt_nt6@log@boost@@@2345@@sources@v2_mt_nt6@log@boost@@QEAA?AVrecord@234@AEBU?$tagged_argument@Useverity@tag@keywords@v2_mt_nt6@log@boost@@$$CBW4severity_level@trivial@456@@aux@parameter@4@@Z) D:\D\SB\MITK-superbuild\MITK-build\Modules\SceneSerialization\mitkSceneIO.obj  
Error   3   error LNK2019: unresolved external symbol "__declspec(dllimport) public: class boost::log::v2_mt_nt6::record __cdecl boost::log::v2_mt_nt6::core::open_record(class boost::log::v2_mt_nt6::attribute_set const &)" (__imp_?open_record@core@v2_mt_nt6@log@boost@@QEAA?AVrecord@234@AEBVattribute_set@234@@Z) referenced in function "protected: class boost::log::v2_mt_nt6::record __cdecl boost::log::v2_mt_nt6::sources::basic_logger<char,class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level>,struct boost::log::v2_mt_nt6::sources::single_thread_model>::open_record_unlocked<struct boost::parameter::aux::tagged_argument<struct boost::log::v2_mt_nt6::keywords::tag::severity,enum boost::log::v2_mt_nt6::trivial::severity_level const > >(struct boost::parameter::aux::tagged_argument<struct boost::log::v2_mt_nt6::keywords::tag::severity,enum boost::log::v2_mt_nt6::trivial::severity_level const > const &)" (??$open_record_unlocked@U?$tagged_argument@Useverity@tag@keywords@v2_mt_nt6@log@boost@@$$CBW4severity_level@trivial@456@@aux@parameter@boost@@@?$basic_logger@DV?$severity_logger@W4severity_level@trivial@v2_mt_nt6@log@boost@@@sources@v2_mt_nt6@log@boost@@Usingle_thread_model@2345@@sources@v2_mt_nt6@log@boost@@IEAA?AVrecord@234@AEBU?$tagged_argument@Useverity@tag@keywords@v2_mt_nt6@log@boost@@$$CBW4severity_level@trivial@456@@aux@parameter@4@@Z) D:\D\SB\MITK-superbuild\MITK-build\Modules\SceneSerialization\mitkSceneIO.obj  
Error   7   error LNK2019: unresolved external symbol "__declspec(dllimport) public: class boost::shared_ptr<class boost::log::v2_mt_nt6::sinks::basic_text_ostream_backend<char> > __cdecl Logger::Log::getDataBackend(void)const " (__imp_?getDataBackend@Log@Logger@@QEBA?AV?$shared_ptr@V?$basic_text_ostream_backend@D@sinks@v2_mt_nt6@log@boost@@@boost@@XZ) referenced in function "public: virtual bool __cdecl mitk::SceneIO::SaveScene(class itk::SmartPointer<class itk::VectorContainer<unsigned int,class itk::SmartPointer<class mitk::DataNode> > const >,class mitk::DataStorage const *,class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &)" (?SaveScene@SceneIO@mitk@@UEAA_NV?$SmartPointer@$$CBV?$VectorContainer@IV?$SmartPointer@VDataNode@mitk@@@itk@@@itk@@@itk@@PEBVDataStorage@2@AEBV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@@Z) D:\D\SB\MITK-superbuild\MITK-build\Modules\SceneSerialization\mitkSceneIO.obj  
Error   8   error LNK2019: unresolved external symbol "__declspec(dllimport) public: static struct boost::log::v2_mt_nt6::aux::stream_provider<char>::stream_compound * __cdecl boost::log::v2_mt_nt6::aux::stream_provider<char>::allocate_compound(class boost::log::v2_mt_nt6::record &)" (__imp_?allocate_compound@?$stream_provider@D@aux@v2_mt_nt6@log@boost@@SAPEAUstream_compound@12345@AEAVrecord@345@@Z) referenced in function "public: __cdecl boost::log::v2_mt_nt6::aux::record_pump<class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level> >::record_pump<class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level> >(class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level> &,class boost::log::v2_mt_nt6::record &)" (??0?$record_pump@V?$severity_logger@W4severity_level@trivial@v2_mt_nt6@log@boost@@@sources@v2_mt_nt6@log@boost@@@aux@v2_mt_nt6@log@boost@@QEAA@AEAV?$severity_logger@W4severity_level@trivial@v2_mt_nt6@log@boost@@@sources@234@AEAVrecord@234@@Z)  D:\D\SB\MITK-superbuild\MITK-build\Modules\SceneSerialization\mitkSceneIO.obj  
Error   9   error LNK2019: unresolved external symbol "__declspec(dllimport) public: static void __cdecl boost::log::v2_mt_nt6::aux::stream_provider<char>::release_compound(struct boost::log::v2_mt_nt6::aux::stream_provider<char>::stream_compound *)" (__imp_?release_compound@?$stream_provider@D@aux@v2_mt_nt6@log@boost@@SAXPEAUstream_compound@12345@@Z) referenced in function "public: __cdecl boost::log::v2_mt_nt6::aux::record_pump<class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level> >::auto_release::~auto_release(void)" (??1auto_release@?$record_pump@V?$severity_logger@W4severity_level@trivial@v2_mt_nt6@log@boost@@@sources@v2_mt_nt6@log@boost@@@aux@v2_mt_nt6@log@boost@@QEAA@XZ) D:\D\SB\MITK-superbuild\MITK-build\Modules\SceneSerialization\mitkSceneIO.obj  
Error   1   error LNK2019: unresolved external symbol "__declspec(dllimport) public: static void __cdecl boost::log::v2_mt_nt6::record_view::public_data::destroy(struct boost::log::v2_mt_nt6::record_view::public_data const *)" (__imp_?destroy@public_data@record_view@v2_mt_nt6@log@boost@@SAXPEBU12345@@Z) referenced in function "public: void __cdecl boost::log::v2_mt_nt6::record::reset(void)" (?reset@record@v2_mt_nt6@log@boost@@QEAAXXZ)  D:\D\SB\MITK-superbuild\MITK-build\Modules\SceneSerialization\mitkSceneIO.obj  
Error   5   error LNK2019: unresolved external symbol "__declspec(dllimport) unsigned __int64 & __cdecl boost::log::v2_mt_nt6::sources::aux::get_severity_level(void)" (__imp_?get_severity_level@aux@sources@v2_mt_nt6@log@boost@@YAAEA_KXZ) referenced in function "public: void __cdecl boost::log::v2_mt_nt6::sources::aux::severity_level<enum boost::log::v2_mt_nt6::trivial::severity_level>::set_value(enum boost::log::v2_mt_nt6::trivial::severity_level)" (?set_value@?$severity_level@W40trivial@v2_mt_nt6@log@boost@@@aux@sources@v2_mt_nt6@log@boost@@QEAAXW4severity_level@trivial@456@@Z)   D:\D\SB\MITK-superbuild\MITK-build\Modules\SceneSerialization\mitkSceneIO.obj  
Error   6   error LNK2019: unresolved external symbol "__declspec(dllimport) unsigned int __cdecl boost::log::v2_mt_nt6::aux::unhandled_exception_count(void)" (__imp_?unhandled_exception_count@aux@v2_mt_nt6@log@boost@@YAIXZ) referenced in function "public: __cdecl boost::log::v2_mt_nt6::aux::record_pump<class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level> >::record_pump<class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level> >(class boost::log::v2_mt_nt6::sources::severity_logger<enum boost::log::v2_mt_nt6::trivial::severity_level> &,class boost::log::v2_mt_nt6::record &)" (??0?$record_pump@V?$severity_logger@W4severity_level@trivial@v2_mt_nt6@log@boost@@@sources@v2_mt_nt6@log@boost@@@aux@v2_mt_nt6@log@boost@@QEAA@AEAV?$severity_logger@W4severity_level@trivial@v2_mt_nt6@log@boost@@@sources@234@AEAVrecord@234@@Z)    D:\D\SB\MITK-superbuild\MITK-build\Modules\SceneSerialization\mitkSceneIO.obj  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2016-05-16 17:09:08 UTC  
> Url: https://github.com/boostorg/log/issues/28#issuecomment-219483522  

See [here](http://www.boost.org/doc/libs/1_61_0/libs/log/doc/html/log/rationale/namespace_mangling.html). You can use Dependency Walker to see the symbol names on Windows. If you see `nt5` in the names then it means Boost.Log was built for Windows XP and later. The reason for this is probably that your compiler doesn't support Windows SDK for Windows 6. You can either try and rebuild Boost with `BOOST_USE_WINAPI_VERSION` defined to 0x0600 or build your own code with `BOOST_USE_WINAPI_VERSION` defined to 0x0501 (in the latter case you say you want Boost to use Windows XP API and not later).

---

## Comment 2

> Username: Cynede  
> Created at: 2016-05-17 06:38:49 UTC  
> Url: https://github.com/boostorg/log/issues/28#issuecomment-219632073  

@Lastique thank you, adding `#define BOOST_USE_WINAPI_VERSION 0x0501` fixes problem but it looks like hack because with some reason `0x0600` is not supported, how can I ensure that boost is built with last WINAPI support?

---

## Comment 3

> Username: Lastique  
> Created at: 2016-05-17 08:45:37 UTC  
> Url: https://github.com/boostorg/log/issues/28#issuecomment-219656370  

You can try building Boost with `define=BOOST_USE_WINAPI_VERSION=0x0600` in the `b2` command line. This will override autodetection. If it succeeds, please tell what compiler and platform you are using and how to detect it so that I can tweak the autodetection.

---

## Comment 4

> Username: Cynede  
> Created at: 2016-05-17 09:25:19 UTC  
> Url: https://github.com/boostorg/log/issues/28#issuecomment-219665165  

@Lastique may you take a look if something is wrong with this b2: https://github.com/MITK/MITK/blob/master/CMakeExternals/Boost.cmake

---

## Comment 5

> Username: Lastique  
> Created at: 2016-05-17 09:33:12 UTC  
> Url: https://github.com/boostorg/log/issues/28#issuecomment-219667028  

Not sure what I'm supposed to see there.

---

## Comment 6

> Username: Cynede  
> Created at: 2016-05-17 09:41:22 UTC  
> Url: https://github.com/boostorg/log/issues/28#issuecomment-219668872  

@Lastique something that possibly breaks `WINAPI_VERSION` detection, otherways it could be system/environment specific or just doesn't work...

---

## Comment 7

> Username: Lastique  
> Created at: 2016-05-17 09:50:15 UTC  
> Updated at: 2016-05-17 09:50:33 UTC  
> Url: https://github.com/boostorg/log/issues/28#issuecomment-219670908  

The default WinAPI version is selected in https://github.com/boostorg/winapi/blob/develop/include/boost/detail/winapi/config.hpp based on compiler being used. You don't seem to define any macros in the cmake script, so I don't think it breaks anything.
