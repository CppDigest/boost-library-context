# #190 - compile error,when use boost log （1.79） [Closed]

> Username: liujingliangPacs  
> Created at: 2022-07-14 09:53:39 UTC  
> Updated at: 2022-07-20 08:36:35 UTC  
> Closed at: 2022-07-14 10:48:18 UTC  
> Url: https://github.com/boostorg/log/issues/190  

Boost Version: 1.79  
  
following is the compile error:  
`Build started...  
1>------ Build started: Project: APcs_Test, Configuration: Debug x64 ------  
1>APcs_Test.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: static void __cdecl boost::log::v2_mt_nt62::record_view::public_data::destroy(struct boost::log::v2_mt_nt62::record_view::public_data const *)" (__imp_?destroy@public_data@record_view@v2_mt_nt62@log@boost@@SAXPEBU12345@@Z) referenced in function "public: void __cdecl boost::log::v2_mt_nt62::record::reset(void)" (?reset@record@v2_mt_nt62@log@boost@@QEAAXXZ)  
1>APcs_Test.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: bool __cdecl boost::log::v2_mt_nt62::core::get_logging_enabled(void)const " (__imp_?get_logging_enabled@core@v2_mt_nt62@log@boost@@QEBA_NXZ) referenced in function "public: class boost::log::v2_mt_nt62::record __cdecl boost::log::v2_mt_nt62::sources::basic_composite_logger<char,class boost::log::v2_mt_nt62::sources::severity_logger_mt<enum boost::log::v2_mt_nt62::trivial::severity_level>,struct boost::log::v2_mt_nt62::sources::multi_thread_model<class boost::log::v2_mt_nt62::aux::light_rw_mutex>,struct boost::log::v2_mt_nt62::sources::features<struct boost::log::v2_mt_nt62::sources::severity<enum boost::log::v2_mt_nt62::trivial::severity_level> > >::open_record<struct boost::parameter::aux::tagged_argument_list_of_1<class boost::parameter::aux::tagged_argument<struct boost::log::v2_mt_nt62::keywords::tag::severity,enum boost::log::v2_mt_nt62::trivial::severity_level const > > >(struct boost::parameter::aux::tagged_argument_list_of_1<class boost::parameter::aux::tagged_argument<struct boost::log::v2_mt_nt62::keywords::tag::severity,enum boost::log::v2_mt_nt62::trivial::severity_level const > > const &)" (??$open_record@U?$tagged_argument_list_of_1@V?$tagged_argument@Useverity@tag@keywords@v2_mt_nt62@log@boost@@$$CBW4severity_level@trivial@456@@aux@parameter@boost@@@aux@parameter@boost@@@?$basic_composite_logger@DV?$severity_logger_mt@W4severity_level@trivial@v2_mt_nt62@log@boost@@@sources@v2_mt_nt62@log@boost@@U?$multi_thread_model@Vlight_rw_mutex@aux@v2_mt_nt62@log@boost@@@2345@U?$features@U?$severity@W4severity_level@trivial@v2_mt_nt62@log@boost@@@sources@v2_mt_nt62@log@boost@@@2345@@sources@v2_mt_nt62@log@boost@@QEAA?AVrecord@234@AEBU?$tagged_argument_list_of_1@V?$tagged_argument@Useverity@tag@keywords@v2_mt_nt62@log@boost@@$$CBW4severity_level@trivial@456@@aux@parameter@boost@@@aux@parameter@4@@Z)  
1>APcs_Test.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: class boost::log::v2_mt_nt62::record __cdecl boost::log::v2_mt_nt62::core::open_record(class boost::log::v2_mt_nt62::attribute_set const &)" (__imp_?open_record@core@v2_mt_nt62@log@boost@@QEAA?AVrecord@234@AEBVattribute_set@234@@Z) referenced in function "protected: class boost::log::v2_mt_nt62::record __cdecl boost::log::v2_mt_nt62::sources::basic_logger<char,class boost::log::v2_mt_nt62::sources::severity_logger_mt<enum boost::log::v2_mt_nt62::trivial::severity_level>,struct boost::log::v2_mt_nt62::sources::multi_thread_model<class boost::log::v2_mt_nt62::aux::light_rw_mutex> >::open_record_unlocked<struct boost::parameter::aux::tagged_argument_list_of_1<class boost::parameter::aux::tagged_argument<struct boost::log::v2_mt_nt62::keywords::tag::severity,enum boost::log::v2_mt_nt62::trivial::severity_level const > > >(struct boost::parameter::aux::tagged_argument_list_of_1<class boost::parameter::aux::tagged_argument<struct boost::log::v2_mt_nt62::keywords::tag::severity,enum boost::log::v2_mt_nt62::trivial::severity_level const > > const &)" (??$open_record_unlocked@U?$tagged_argument_list_of_1@V?$tagged_argument@Useverity@tag@keywords@v2_mt_nt62@log@boost@@$$CBW4severity_level@trivial@456@@aux@parameter@boost@@@aux@parameter@boost@@@?$basic_logger@DV?$severity_logger_mt@W4severity_level@trivial@v2_mt_nt62@log@boost@@@sources@v2_mt_nt62@log@boost@@U?$multi_thread_model@Vlight_rw_mutex@aux@v2_mt_nt62@log@boost@@@2345@@sources@v2_mt_nt62@log@boost@@IEAA?AVrecord@234@AEBU?$tagged_argument_list_of_1@V?$tagged_argument@Useverity@tag@keywords@v2_mt_nt62@log@boost@@$$CBW4severity_level@trivial@456@@aux@parameter@boost@@@aux@parameter@4@@Z)  
1>APcs_Test.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) private: void __cdecl boost::log::v2_mt_nt62::core::push_record_move(class boost::log::v2_mt_nt62::record &)" (__imp_?push_record_move@core@v2_mt_nt62@log@boost@@AEAAXAEAVrecord@234@@Z) referenced in function "public: void __cdecl boost::log::v2_mt_nt62::core::push_record(class boost::log::v2_mt_nt62::record &&)" (?push_record@core@v2_mt_nt62@log@boost@@QEAAX$$QEAVrecord@234@@Z)  
1>APcs_Test.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) unsigned __int64 & __cdecl boost::log::v2_mt_nt62::sources::aux::get_severity_level(void)" (__imp_?get_severity_level@aux@sources@v2_mt_nt62@log@boost@@YAAEA_KXZ) referenced in function "public: void __cdecl boost::log::v2_mt_nt62::sources::aux::severity_level<enum boost::log::v2_mt_nt62::trivial::severity_level>::set_value(enum boost::log::v2_mt_nt62::trivial::severity_level)" (?set_value@?$severity_level@W40trivial@v2_mt_nt62@log@boost@@@aux@sources@v2_mt_nt62@log@boost@@QEAAXW4severity_level@trivial@456@@Z)  
1>APcs_Test.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: static class boost::log::v2_mt_nt62::sources::severity_logger_mt<enum boost::log::v2_mt_nt62::trivial::severity_level> & __cdecl boost::log::v2_mt_nt62::trivial::logger::get(void)" (__imp_?get@logger@trivial@v2_mt_nt62@log@boost@@SAAEAV?$severity_logger_mt@W4severity_level@trivial@v2_mt_nt62@log@boost@@@sources@345@XZ) referenced in function "bool __cdecl test_fuc(void)" (?test_fuc@@YA_NXZ)  
1>APcs_Test.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: static struct boost::log::v2_mt_nt62::aux::stream_provider<char>::stream_compound * __cdecl boost::log::v2_mt_nt62::aux::stream_provider<char>::allocate_compound(class boost::log::v2_mt_nt62::record &)" (__imp_?allocate_compound@?$stream_provider@D@aux@v2_mt_nt62@log@boost@@SAPEAUstream_compound@12345@AEAVrecord@345@@Z) referenced in function "public: __cdecl boost::log::v2_mt_nt62::aux::record_pump<class boost::log::v2_mt_nt62::sources::severity_logger_mt<enum boost::log::v2_mt_nt62::trivial::severity_level> >::record_pump<class boost::log::v2_mt_nt62::sources::severity_logger_mt<enum boost::log::v2_mt_nt62::trivial::severity_level> >(class boost::log::v2_mt_nt62::sources::severity_logger_mt<enum boost::log::v2_mt_nt62::trivial::severity_level> &,class boost::log::v2_mt_nt62::record &)" (??0?$record_pump@V?$severity_logger_mt@W4severity_level@trivial@v2_mt_nt62@log@boost@@@sources@v2_mt_nt62@log@boost@@@aux@v2_mt_nt62@log@boost@@QEAA@AEAV?$severity_logger_mt@W4severity_level@trivial@v2_mt_nt62@log@boost@@@sources@234@AEAVrecord@234@@Z)  
1>APcs_Test.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: static void __cdecl boost::log::v2_mt_nt62::aux::stream_provider<char>::release_compound(struct boost::log::v2_mt_nt62::aux::stream_provider<char>::stream_compound *)" (__imp_?release_compound@?$stream_provider@D@aux@v2_mt_nt62@log@boost@@SAXPEAUstream_compound@12345@@Z) referenced in function "public: __cdecl boost::log::v2_mt_nt62::aux::record_pump<class boost::log::v2_mt_nt62::sources::severity_logger_mt<enum boost::log::v2_mt_nt62::trivial::severity_level> >::auto_release::~auto_release(void)" (??1auto_release@?$record_pump@V?$severity_logger_mt@W4severity_level@trivial@v2_mt_nt62@log@boost@@@sources@v2_mt_nt62@log@boost@@@aux@v2_mt_nt62@log@boost@@QEAA@XZ)  
1>E:\SRC\Repository\APcs_Scanning_2022\Bin\x64\Debug\APcs_Test.exe : fatal error LNK1120: 8 unresolved externals  
1>Done building project "APcs_Test.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
`  
my source code is the boost log tutorial：  
`#include <boost/log/trivial.hpp>  
  
  
bool test_fuc()  
{  
    BOOST_LOG_TRIVIAL(trace) << "A trace severity message";  
    BOOST_LOG_TRIVIAL(debug) << "A debug severity message";  
    BOOST_LOG_TRIVIAL(info) << "An informational severity message";  
    BOOST_LOG_TRIVIAL(warning) << "A warning severity message";  
    BOOST_LOG_TRIVIAL(error) << "An error severity message";  
    BOOST_LOG_TRIVIAL(fatal) << "A fatal severity message";  
  
    return true;  
}  
int main()  
{  
    test_fuc();  
    //  
    system("pause");  
    return 0;  
};  
`

---

## Comment 1

> Username: Lastique  
> Created at: 2022-07-14 10:48:18 UTC  
> Url: https://github.com/boostorg/log/issues/190#issuecomment-1184291775  

Assuming that you're linking with Boost.Log properly, most likely you're defining different target Windows version macros when building Boost and your application. Check what values you define in `_WIN32_WINNT` and/or `BOOST_USE_WINAPI_VERSION`, these values must be the same when you build Boost and your code. See [here](https://stackoverflow.com/questions/71120362/boost-log-linker-errors-lnk2001-lnk2019-unresolved-external-symbol-after-upgra/71124787#71124787).

---

## Comment 2

> Username: liujingliangPacs  
> Created at: 2022-07-16 04:22:45 UTC  
> Url: https://github.com/boostorg/log/issues/190#issuecomment-1186085809  

Thank you for you reply!  
I have used lots of version of boost.log; Everything is OK;   
for example version 1.76, I can compile my code successfully with it;  
But, after I update to version boost.log 1.79, the compile error occurs;  
All my used boost binary files(*.dll and *.lib) are downloaded from "https://sourceforge.net/projects/boost/files/boost-binaries/".

---

## Comment 3

> Username: Lastique  
> Created at: 2022-07-16 07:01:54 UTC  
> Updated at: 2022-07-16 07:02:49 UTC  
> Url: https://github.com/boostorg/log/issues/190#issuecomment-1186104184  

Boost.Log 1.78 [started tagging Windows 8.1](https://www.boost.org/doc/libs/1_79_0/libs/log/doc/html/log/changelog.html#log.changelog.2_23__boost_1_78), which isn't enabled by default when building Boost with default settings (which is how the official binaries are built). Your code either defines `_WIN32_WINNT` to `0x0602` or higher or uses a Windows SDK that defines `_WIN32_WINNT` like that.

---

## Comment 4

> Username: liujingliangPacs  
> Created at: 2022-07-20 08:25:18 UTC  
> Updated at: 2022-07-20 08:36:35 UTC  
> Url: https://github.com/boostorg/log/issues/190#issuecomment-1189979305  

Thank you very much that I finally compiled it myself;  
I should have added the "-a" option, otherwise it might not recompiled.  
Now that's solved, thank you, thank you.  
  
following is the command line:  
b2 stage --toolset=msvc-14.3 --with-log --stagedir="d:\Program\boost_Lib" link=shared runtime-link=shared threading=multi variant=debug address-model=64 define=_WIN32_WINNT=0x0601 define=BOOST_USE_WINAPI_VERSION=0x0601 -a  
  
following is added to my code:  
#define _WIN32_WINNT 0x0601   
#define BOOST_USE_WINAPI_VERSION 0x0601
