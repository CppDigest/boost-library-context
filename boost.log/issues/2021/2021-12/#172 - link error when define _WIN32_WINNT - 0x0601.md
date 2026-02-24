# #172 - link error when define _WIN32_WINNT > 0x0601 [Closed]

> Username: UMU618  
> Created at: 2021-12-15 07:46:40 UTC  
> Updated at: 2021-12-15 10:40:48 UTC  
> Closed at: 2021-12-15 08:48:34 UTC  
> Url: https://github.com/boostorg/log/issues/172  

Compile with VS2022.  
After upgrade to 1.78, I got link error, when define _WIN32_WINNT=0x0602 or  _WIN32_WINNT=0x0A00.  
  
It's OK on 1.77 with _WIN32_WINNT=0x0602.  
And it's OK on 1.78 with _WIN32_WINNT=0x0601.  
  
```  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static void __cdecl boost::log::v2s_mt_nt62::attribute::impl::operator delete(void *,unsigned __int64)" (??3impl@attribute@v2s_mt_nt62@log@boost@@SAXPEAX_K@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "private: static unsigned int __cdecl boost::log::v2s_mt_nt62::attribute_name::get_id_from_string(char const *)" (?get_id_from_string@attribute_name@v2s_mt_nt62@log@boost@@CAIPEBD@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: __cdecl boost::log::v2s_mt_nt62::attribute_set::~attribute_set(void)" (??1attribute_set@v2s_mt_nt62@log@boost@@QEAA@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: struct std::pair<class boost::log::v2s_mt_nt62::attribute_set::iter<0>,bool> __cdecl boost::log::v2s_mt_nt62::attribute_set::insert(class boost::log::v2s_mt_nt62::attribute_name,class boost::log::v2s_mt_nt62::attribute const &)" (?insert@attribute_set@v2s_mt_nt62@log@boost@@QEAA?AU?$pair@V?$iter@$0A@@attribute_set@v2s_mt_nt62@log@boost@@_N@std@@Vattribute_name@234@AEBVattribute@234@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: class boost::log::v2s_mt_nt62::attribute_value_set::const_iterator __cdecl boost::log::v2s_mt_nt62::attribute_value_set::find(class boost::log::v2s_mt_nt62::attribute_name)const " (?find@attribute_value_set@v2s_mt_nt62@log@boost@@QEBA?AVconst_iterator@1234@Vattribute_name@234@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: class boost::log::v2s_mt_nt62::attribute_value_set::const_iterator __cdecl boost::log::v2s_mt_nt62::attribute_value_set::end(void)const " (?end@attribute_value_set@v2s_mt_nt62@log@boost@@QEBA?AVconst_iterator@1234@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static void __cdecl boost::log::v2s_mt_nt62::record_view::public_data::destroy(struct boost::log::v2s_mt_nt62::record_view::public_data const *)" (?destroy@public_data@record_view@v2s_mt_nt62@log@boost@@SAXPEBU12345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "bool __cdecl boost::log::v2s_mt_nt62::aux::code_convert_impl(wchar_t const *,unsigned __int64,class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > &,unsigned __int64,class std::locale const &)" (?code_convert_impl@aux@v2s_mt_nt62@log@boost@@YA_NPEB_W_KAEAV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@1AEBVlocale@6@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "private: void __cdecl boost::log::v2s_mt_nt62::aux::once_block_sentry::rollback(void)" (?rollback@once_block_sentry@aux@v2s_mt_nt62@log@boost@@AEAAXXZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "private: bool __cdecl boost::log::v2s_mt_nt62::aux::once_block_sentry::enter_once_block(void)const " (?enter_once_block@once_block_sentry@aux@v2s_mt_nt62@log@boost@@AEBA_NXZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "class boost::log::v2s_mt_nt62::attribute_name __cdecl boost::log::v2s_mt_nt62::aux::default_attribute_names::message(void)" (?message@default_attribute_names@aux@v2s_mt_nt62@log@boost@@YA?AVattribute_name@345@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::aux::atomic_based_event::set_signalled(void)" (?set_signalled@atomic_based_event@aux@v2s_mt_nt62@log@boost@@QEAAXXZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::aux::atomic_based_event::wait(void)" (?wait@atomic_based_event@aux@v2s_mt_nt62@log@boost@@QEAAXXZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "private: void __cdecl boost::log::v2s_mt_nt62::core::push_record_move(class boost::log::v2s_mt_nt62::record &)" (?push_record_move@core@v2s_mt_nt62@log@boost@@AEAAXAEAVrecord@234@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "class boost::log::v2s_mt_nt62::aux::id<struct boost::log::v2s_mt_nt62::aux::process> __cdecl boost::log::v2s_mt_nt62::aux::this_process::get_id(void)" (?get_id@this_process@aux@v2s_mt_nt62@log@boost@@YA?AV?$id@Uprocess@aux@v2s_mt_nt62@log@boost@@@2345@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "class boost::log::v2s_mt_nt62::aux::id<struct boost::log::v2s_mt_nt62::aux::thread> const & __cdecl boost::log::v2s_mt_nt62::aux::this_thread::get_id(void)" (?get_id@this_thread@aux@v2s_mt_nt62@log@boost@@YAAEBV?$id@Uthread@aux@v2s_mt_nt62@log@boost@@@2345@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static void * __cdecl boost::log::v2s_mt_nt62::attribute::impl::operator new(unsigned __int64)" (??2impl@attribute@v2s_mt_nt62@log@boost@@SAPEAX_K@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static class boost::shared_ptr<class boost::log::v2s_mt_nt62::core> __cdecl boost::log::v2s_mt_nt62::core::get(void)" (?get@core@v2s_mt_nt62@log@boost@@SA?AV?$shared_ptr@Vcore@v2s_mt_nt62@log@boost@@@4@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: struct std::pair<class boost::log::v2s_mt_nt62::attribute_set::iter<0>,bool> __cdecl boost::log::v2s_mt_nt62::core::add_global_attribute(class boost::log::v2s_mt_nt62::attribute_name const &,class boost::log::v2s_mt_nt62::attribute const &)" (?add_global_attribute@core@v2s_mt_nt62@log@boost@@QEAA?AU?$pair@V?$iter@$0A@@attribute_set@v2s_mt_nt62@log@boost@@_N@std@@AEBVattribute_name@234@AEBVattribute@234@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "class boost::log::v2s_mt_nt62::attribute_name __cdecl boost::log::v2s_mt_nt62::aux::default_attribute_names::line_id(void)" (?line_id@default_attribute_names@aux@v2s_mt_nt62@log@boost@@YA?AVattribute_name@345@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "class boost::log::v2s_mt_nt62::attribute_name __cdecl boost::log::v2s_mt_nt62::aux::default_attribute_names::timestamp(void)" (?timestamp@default_attribute_names@aux@v2s_mt_nt62@log@boost@@YA?AVattribute_name@345@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "class boost::log::v2s_mt_nt62::attribute_name __cdecl boost::log::v2s_mt_nt62::aux::default_attribute_names::process_id(void)" (?process_id@default_attribute_names@aux@v2s_mt_nt62@log@boost@@YA?AVattribute_name@345@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "class boost::log::v2s_mt_nt62::attribute_name __cdecl boost::log::v2s_mt_nt62::aux::default_attribute_names::thread_id(void)" (?thread_id@default_attribute_names@aux@v2s_mt_nt62@log@boost@@YA?AVattribute_name@345@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::core::set_filter(class boost::log::v2s_mt_nt62::filter const &)" (?set_filter@core@v2s_mt_nt62@log@boost@@QEAAXAEBVfilter@234@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::sinks::basic_text_ostream_backend<char>::add_stream(class boost::shared_ptr<class std::basic_ostream<char,struct std::char_traits<char> > > const &)" (?add_stream@?$basic_text_ostream_backend@D@sinks@v2s_mt_nt62@log@boost@@QEAAXAEBV?$shared_ptr@V?$basic_ostream@DU?$char_traits@D@std@@@std@@@5@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::sinks::basic_text_ostream_backend<char>::auto_flush(bool)" (?auto_flush@?$basic_text_ostream_backend@D@sinks@v2s_mt_nt62@log@boost@@QEAAX_N@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::sinks::basic_text_ostream_backend<char>::set_auto_newline_mode(enum boost::log::v2s_mt_nt62::sinks::auto_newline_mode)" (?set_auto_newline_mode@?$basic_text_ostream_backend@D@sinks@v2s_mt_nt62@log@boost@@QEAAXW4auto_newline_mode@2345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::core::add_sink(class boost::shared_ptr<class boost::log::v2s_mt_nt62::sinks::sink> const &)" (?add_sink@core@v2s_mt_nt62@log@boost@@QEAAXAEBV?$shared_ptr@Vsink@sinks@v2s_mt_nt62@log@boost@@@4@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::sinks::syslog_backend::set_severity_mapper(class boost::log::v2s_mt_nt62::aux::light_function<enum boost::log::v2s_mt_nt62::sinks::syslog::level __cdecl(class boost::log::v2s_mt_nt62::record_view const &)> const &)" (?set_severity_mapper@syslog_backend@sinks@v2s_mt_nt62@log@boost@@QEAAXAEBV?$light_function@$$A6A?AW4level@syslog@sinks@v2s_mt_nt62@log@boost@@AEBVrecord_view@456@@Z@aux@345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::core::flush(void)" (?flush@core@v2s_mt_nt62@log@boost@@QEAAXXZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static bool __cdecl boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::try_pop(class boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl *,struct boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::node_base * &,struct boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::node_base * &)" (?try_pop@threadsafe_queue_impl@aux@v2s_mt_nt62@log@boost@@SA_NPEAV12345@AEAPEAUnode_base@12345@1@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static void __cdecl boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::push(class boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl *,struct boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::node_base *)" (?push@threadsafe_queue_impl@aux@v2s_mt_nt62@log@boost@@SAXPEAV12345@PEAUnode_base@12345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static struct boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::node_base * __cdecl boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::reset_last_node(class boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl *)" (?reset_last_node@threadsafe_queue_impl@aux@v2s_mt_nt62@log@boost@@SAPEAUnode_base@12345@PEAV12345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static void __cdecl boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::destroy(class boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl *)" (?destroy@threadsafe_queue_impl@aux@v2s_mt_nt62@log@boost@@SAXPEAV12345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static class boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl * __cdecl boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::create(struct boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::node_base *)" (?create@threadsafe_queue_impl@aux@v2s_mt_nt62@log@boost@@SAPEAV12345@PEAUnode_base@12345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static void __cdecl boost::log::v2s_mt_nt62::aux::stream_provider<char>::release_compound(struct boost::log::v2s_mt_nt62::aux::stream_provider<char>::stream_compound *)" (?release_compound@?$stream_provider@D@aux@v2s_mt_nt62@log@boost@@SAXPEAUstream_compound@12345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "class boost::log::v2s_mt_nt62::attribute_name __cdecl boost::log::v2s_mt_nt62::aux::default_attribute_names::severity(void)" (?severity@default_attribute_names@aux@v2s_mt_nt62@log@boost@@YA?AVattribute_name@345@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static bool __cdecl boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl::unsafe_empty(class boost::log::v2s_mt_nt62::aux::threadsafe_queue_impl const *)" (?unsafe_empty@threadsafe_queue_impl@aux@v2s_mt_nt62@log@boost@@SA_NPEBV12345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: __cdecl boost::log::v2s_mt_nt62::attribute_set::attribute_set(void)" (??0attribute_set@v2s_mt_nt62@log@boost@@QEAA@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "unsigned __int64 & __cdecl boost::log::v2s_mt_nt62::sources::aux::get_severity_level(void)" (?get_severity_level@aux@sources@v2s_mt_nt62@log@boost@@YAAEA_KXZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: bool __cdecl boost::log::v2s_mt_nt62::core::get_logging_enabled(void)const " (?get_logging_enabled@core@v2s_mt_nt62@log@boost@@QEBA_NXZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::sinks::syslog_backend::consume(class boost::log::v2s_mt_nt62::record_view const &,class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &)" (?consume@syslog_backend@sinks@v2s_mt_nt62@log@boost@@QEAAXAEBVrecord_view@345@AEBV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::sinks::basic_text_ostream_backend<char>::consume(class boost::log::v2s_mt_nt62::record_view const &,class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &)" (?consume@?$basic_text_ostream_backend@D@sinks@v2s_mt_nt62@log@boost@@QEAAXAEBVrecord_view@345@AEBV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static struct boost::log::v2s_mt_nt62::aux::stream_provider<char>::stream_compound * __cdecl boost::log::v2s_mt_nt62::aux::stream_provider<char>::allocate_compound(class boost::log::v2s_mt_nt62::record &)" (?allocate_compound@?$stream_provider@D@aux@v2s_mt_nt62@log@boost@@SAPEAUstream_compound@12345@AEAVrecord@345@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "void __cdecl boost::log::v2s_mt_nt62::aux::attach_attribute_name_info(class boost::exception &,class boost::log::v2s_mt_nt62::attribute_name const &)" (?attach_attribute_name_info@aux@v2s_mt_nt62@log@boost@@YAXAEAVexception@4@AEBVattribute_name@234@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::sinks::basic_text_ostream_backend<char>::flush(void)" (?flush@?$basic_text_ostream_backend@D@sinks@v2s_mt_nt62@log@boost@@QEAAXXZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: __cdecl boost::log::v2s_mt_nt62::sinks::basic_text_ostream_backend<char>::basic_text_ostream_backend<char>(void)" (??0?$basic_text_ostream_backend@D@sinks@v2s_mt_nt62@log@boost@@QEAA@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: class boost::log::v2s_mt_nt62::record __cdecl boost::log::v2s_mt_nt62::core::open_record(class boost::log::v2s_mt_nt62::attribute_set const &)" (?open_record@core@v2s_mt_nt62@log@boost@@QEAA?AVrecord@234@AEBVattribute_set@234@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: static void __cdecl boost::log::v2s_mt_nt62::unexpected_call::throw_(char const *,unsigned __int64,char const *)" (?throw_@unexpected_call@v2s_mt_nt62@log@boost@@SAXPEBD_K0@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: __cdecl boost::log::v2s_mt_nt62::sinks::basic_text_ostream_backend<char>::~basic_text_ostream_backend<char>(void)" (??1?$basic_text_ostream_backend@D@sinks@v2s_mt_nt62@log@boost@@QEAA@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "private: void __cdecl boost::log::v2s_mt_nt62::sinks::syslog_backend::construct(enum boost::log::v2s_mt_nt62::sinks::syslog::facility,enum boost::log::v2s_mt_nt62::sinks::syslog::impl_types,enum boost::log::v2s_mt_nt62::sinks::ip_versions,class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &)" (?construct@syslog_backend@sinks@v2s_mt_nt62@log@boost@@AEAAXW4facility@syslog@2345@W4impl_types@72345@W4ip_versions@2345@AEBV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@@Z)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: __cdecl boost::log::v2s_mt_nt62::sinks::syslog_backend::~syslog_backend(void)" (??1syslog_backend@sinks@v2s_mt_nt62@log@boost@@QEAA@XZ)  
1>my_program.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::log::v2s_mt_nt62::aux::once_block_sentry::commit(void)" (?commit@once_block_sentry@aux@v2s_mt_nt62@log@boost@@QEAAXXZ)  
1>my_program.exe : fatal error LNK1120: 53 unresolved externals  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-15 08:48:34 UTC  
> Url: https://github.com/boostorg/log/issues/172#issuecomment-994507942  

You need to build Boost for the same Windows version as your own code. For example, add `define=_WIN32_WINNT=0x0602` to your `b2` command line.

---

## Comment 2

> Username: UMU618  
> Created at: 2021-12-15 09:25:21 UTC  
> Updated at: 2021-12-15 09:26:05 UTC  
> Url: https://github.com/boostorg/log/issues/172#issuecomment-994565915  

> You need to build Boost for the same Windows version as your own code. For example, add `define=_WIN32_WINNT=0x0602` to your `b2` command line.  
  
If I have two projects, one has `define=_WIN32_WINNT=0x0602`, the other has `define=_WIN32_WINNT=0x0A00`, so I need to build Boost two times? I wonder if there is an easy way that I only build Boost one time?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-12-15 10:40:48 UTC  
> Url: https://github.com/boostorg/log/issues/172#issuecomment-994663035  

Currently, Boost.Log only distinguishes between Windows < Vista, < 8 and >= 8. This may change in the future. So, yes, you need to build Boost for each target Windows version you use. Builds for different target Windows versions may not interoperate correctly.  
  
You may build Boost once, if you target a single (lower) Windows version. It is possible to configure Boost to target a Windows version different from your code by defining `BOOST_USE_WINAPI_VERSION` to the version number similar to `_WIN32_WINNT`. Though this will only work for libraries that rely on Boost.WinAPI. Boost.Log and its dependencies, to my knowledge, do. Note that this may limit the functionality Boost will use and provide.
