# #116 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 00:24:05 UTC  
> Updated at: 2020-05-04 14:20:27 UTC  
> Closed at: 2020-05-04 14:20:27 UTC  
> Url: https://github.com/boostorg/log/issues/116  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted:  
  
<pre>  
./boost/log/attributes/attribute_value.hpp:101:33: warning: ‘virtual boost::log::v2_mt_posix::attribute_value boost::log::v2_mt_posix::attribute_value::impl::get_value()’ can be marked override [-Wsuggest-override]  
./boost/log/attributes/attribute_value_impl.hpp:76:18: warning: ‘bool boost::log::v2_mt_posix::attributes::attribute_value_impl<T>::dispatch(boost::log::v2_mt_posix::type_dispatcher&) [with T = boost::log::v2_mt_posix::trivial::severity_level]’ can be marked override [-Wsuggest-override]  
./boost/log/attributes/attribute_value_impl.hpp:91:27: warning: ‘boost::typeindex::type_index boost::log::v2_mt_posix::attributes::attribute_value_impl<T>::get_type() const [with T = boost::log::v2_mt_posix::trivial::severity_level; boost::typeindex::type_index = boost::typeindex::stl_type_index]’ can be marked override [-Wsuggest-override]  
./boost/log/detail/attachable_sstream_buf.hpp:247:9: warning: ‘int boost::log::v2_mt_posix::aux::basic_ostringstreambuf<CharT, TraitsT, AllocatorT>::sync() [with CharT = char; TraitsT = std::char_traits<char>; AllocatorT = std::allocator<char>]’ can be marked override [-Wsuggest-override]  
./boost/log/detail/attachable_sstream_buf.hpp:259:14: warning: ‘boost::log::v2_mt_posix::aux::basic_ostringstreambuf<CharT, TraitsT, AllocatorT>::int_type boost::log::v2_mt_posix::aux::basic_ostringstreambuf<CharT, TraitsT, AllocatorT>::overflow(boost::log::v2_mt_posix::aux::basic_ostringstreambuf<CharT, TraitsT, AllocatorT>::int_type) [with CharT = char; TraitsT = std::char_traits<char>; AllocatorT = std::allocator<char>; boost::log::v2_mt_posix::aux::basic_ostringstreambuf<CharT, TraitsT, AllocatorT>::int_type = int]’ can be marked override [-Wsuggest-override]  
./boost/log/detail/attachable_sstream_buf.hpp:271:21: warning: ‘std::streamsize boost::log::v2_mt_posix::aux::basic_ostringstreambuf<CharT, TraitsT, AllocatorT>::xsputn(const char_type*, std::streamsize) [with CharT = char; TraitsT = std::char_traits<char>; AllocatorT = std::allocator<char>; std::streamsize = long int; boost::log::v2_mt_posix::aux::basic_ostringstreambuf<CharT, TraitsT, AllocatorT>::char_type = char]’ can be marked override [-Wsuggest-override]  
./boost/log/detail/date_time_format_parser.hpp:353:18: warning: ‘void boost::log::v2_mt_posix::aux::date_time_format_parser_callback<CharT>::on_literal(const boost::iterator_range<const Char*>&) [with CharT = char]’ can be marked override [-Wsuggest-override]  
./boost/log/detail/date_time_format_parser.hpp:360:18: warning: ‘void boost::log::v2_mt_posix::aux::date_time_format_parser_callback<CharT>::on_placeholder(const boost::iterator_range<const Char*>&) [with CharT = char]’ can be marked override [-Wsuggest-override]  
./boost/log/exceptions.hpp:91:17: warning: ‘virtual const char* boost::log::v2_mt_posix::bad_alloc::what() const’ can be marked override [-Wsuggest-override]  
./boost/log/exceptions.hpp:91:17: warning: ‘virtual const char* boost::log::v2s_mt_posix::bad_alloc::what() const’ can be marked override [-Wsuggest-override]  
./boost/log/sinks/async_frontend.hpp:331:10: warning: ‘void boost::log::v2_mt_posix::sinks::asynchronous_sink<SinkBackendT, QueueingStrategyT>::consume(const boost::log::v2_mt_posix::record_view&) [with SinkBackendT = boost::log::v2_mt_posix::sinks::basic_text_ostream_backend<char>; QueueingStrategyT = boost::log::v2_mt_posix::sinks::unbounded_fifo_queue]’ can be marked override [-Wsuggest-override]  
./boost/log/sinks/async_frontend.hpp:346:10: warning: ‘bool boost::log::v2_mt_posix::sinks::asynchronous_sink<SinkBackendT, QueueingStrategyT>::try_consume(const boost::log::v2_mt_posix::record_view&) [with SinkBackendT = boost::log::v2_mt_posix::sinks::basic_text_ostream_backend<char>; QueueingStrategyT = boost::log::v2_mt_posix::sinks::unbounded_fifo_queue]’ can be marked override [-Wsuggest-override]  
./boost/log/sinks/async_frontend.hpp:442:10: warning: ‘void boost::log::v2_mt_posix::sinks::asynchronous_sink<SinkBackendT, QueueingStrategyT>::flush() [with SinkBackendT = boost::log::v2_mt_posix::sinks::basic_text_ostream_backend<char>; QueueingStrategyT = boost::log::v2_mt_posix::sinks::unbounded_fifo_queue]’ can be marked override [-Wsuggest-override]  
./boost/log/sinks/basic_sink_frontend.hpp:125:10: warning: ‘virtual bool boost::log::v2_mt_posix::sinks::basic_sink_frontend::will_consume(const boost::log::v2_mt_posix::attribute_value_set&)’ can be marked override [-Wsuggest-override]  
./boost/log/sinks/sync_frontend.hpp:151:10: warning: ‘void boost::log::v2_mt_posix::sinks::synchronous_sink<SinkBackendT>::consume(const boost::log::v2_mt_posix::record_view&) [with SinkBackendT = boost::log::v2_mt_posix::sinks::basic_text_ostream_backend<char>]’ can be marked override [-Wsuggest-override]  
./boost/log/sinks/sync_frontend.hpp:159:10: warning: ‘bool boost::log::v2_mt_posix::sinks::synchronous_sink<SinkBackendT>::try_consume(const boost::log::v2_mt_posix::record_view&) [with SinkBackendT = boost::log::v2_mt_posix::sinks::basic_text_ostream_backend<char>]’ can be marked override [-Wsuggest-override]  
./boost/log/sinks/sync_frontend.hpp:169:10: warning: ‘void boost::log::v2_mt_posix::sinks::synchronous_sink<SinkBackendT>::flush() [with SinkBackendT = boost::log::v2_mt_posix::sinks::basic_text_ostream_backend<char>]’ can be marked override [-Wsuggest-override]  
./boost/log/sources/severity_feature.hpp:70:18: warning: ‘bool boost::log::v2_mt_posix::sources::aux::severity_level<LevelT>::impl::dispatch(boost::log::v2_mt_posix::type_dispatcher&) [with LevelT = boost::log::v2_mt_posix::trivial::severity_level]’ can be marked override [-Wsuggest-override]  
./boost/log/sources/severity_feature.hpp:83:52: warning: ‘boost::intrusive_ptr<boost::log::v2_mt_posix::attribute_value::impl> boost::log::v2_mt_posix::sources::aux::severity_level<LevelT>::impl::detach_from_thread() [with LevelT = boost::log::v2_mt_posix::trivial::severity_level]’ can be marked override [-Wsuggest-override]  
</pre>  
  
Depends on boostorg/config#329.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-04-13 00:24:42 UTC  
> Url: https://github.com/boostorg/log/issues/116#issuecomment-612698339  

Continuation of warnings list:  
  
<pre>  
libs/log/src/default_sink.hpp:60:10: warning: ‘virtual bool boost::log::v2_mt_posix::sinks::aux::default_sink::will_consume(const boost::log::v2_mt_posix::attribute_value_set&)’ can be marked override [-Wsuggest-override]  
libs/log/src/default_sink.hpp:61:10: warning: ‘virtual void boost::log::v2_mt_posix::sinks::aux::default_sink::consume(const boost::log::v2_mt_posix::record_view&)’ can be marked override [-Wsuggest-override]  
libs/log/src/default_sink.hpp:62:10: warning: ‘virtual void boost::log::v2_mt_posix::sinks::aux::default_sink::flush()’ can be marked override [-Wsuggest-override]  
libs/log/src/named_scope.cpp:110:31: warning: ‘virtual boost::typeindex::type_index boost::log::v2_mt_posix::attributes::{anonymous}::named_scope_value::get_type() const’ can be marked override [-Wsuggest-override]  
libs/log/src/named_scope.cpp:114:48: warning: ‘virtual boost::intrusive_ptr<boost::log::v2_mt_posix::attribute_value::impl> boost::log::v2_mt_posix::attributes::{anonymous}::named_scope_value::detach_from_thread()’ can be marked override [-Wsuggest-override]  
libs/log/src/named_scope.cpp:188:21: warning: ‘virtual boost::log::v2_mt_posix::attribute_value boost::log::v2_mt_posix::attributes::named_scope::impl::get_value()’ can be marked override [-Wsuggest-override]  
libs/log/src/named_scope.cpp:94:14: warning: ‘virtual bool boost::log::v2_mt_posix::attributes::{anonymous}::named_scope_value::dispatch(boost::log::v2_mt_posix::type_dispatcher&)’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.cpp:226:8: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_equality_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.cpp:233:8: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_inequality_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.cpp:240:8: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_less_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.cpp:247:8: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_greater_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.cpp:254:8: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_less_or_equal_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.cpp:261:8: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_greater_or_equal_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.cpp:268:8: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_custom_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.hpp:73:20: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_equality_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.hpp:75:20: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_inequality_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.hpp:77:20: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_less_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.hpp:79:20: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_greater_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.hpp:81:20: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_less_or_equal_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.hpp:83:20: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_greater_or_equal_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_filter_factory.hpp:86:20: warning: ‘boost::log::v2_mt_posix::filter boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::on_custom_relation(const boost::log::v2_mt_posix::attribute_name&, const string_type&, const string_type&) [with CharT = char; boost::log::v2_mt_posix::aux::default_filter_factory<CharT>::string_type = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_formatter_factory.cpp:267:1: warning: ‘boost::log::v2_mt_posix::aux::default_formatter_factory<CharT>::formatter_type boost::log::v2_mt_posix::aux::default_formatter_factory<CharT>::create_formatter(const boost::log::v2_mt_posix::attribute_name&, const args_map&) [with CharT = char; boost::log::v2_mt_posix::aux::default_formatter_factory<CharT>::formatter_type = boost::log::v2_mt_posix::basic_formatter<char>; boost::log::v2_mt_posix::aux::default_formatter_factory<CharT>::args_map = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char>, std::less<std::__cxx11::basic_string<char> >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/default_formatter_factory.hpp:48:28: warning: ‘boost::log::v2_mt_posix::aux::default_formatter_factory<CharT>::formatter_type boost::log::v2_mt_posix::aux::default_formatter_factory<CharT>::create_formatter(const boost::log::v2_mt_posix::attribute_name&, const args_map&) [with CharT = char; boost::log::v2_mt_posix::aux::default_formatter_factory<CharT>::formatter_type = boost::log::v2_mt_posix::basic_formatter<char>; boost::log::v2_mt_posix::aux::default_formatter_factory<CharT>::args_map = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char>, std::less<std::__cxx11::basic_string<char> >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/init_from_settings.cpp:436:31: warning: ‘boost::shared_ptr<boost::log::v2_mt_posix::sinks::sink> boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<CharT>::create_sink(const settings_section&) [with CharT = char; boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<CharT>::settings_section = boost::log::v2_mt_posix::basic_settings_section<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/init_from_settings.cpp:456:31: warning: ‘boost::shared_ptr<boost::log::v2_mt_posix::sinks::sink> boost::log::v2_mt_posix::{anonymous}::default_text_file_sink_factory<CharT>::create_sink(const settings_section&) [with CharT = char; boost::log::v2_mt_posix::{anonymous}::default_text_file_sink_factory<CharT>::settings_section = boost::log::v2_mt_posix::basic_settings_section<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/setup/init_from_settings.cpp:581:31: warning: ‘boost::shared_ptr<boost::log::v2_mt_posix::sinks::sink> boost::log::v2_mt_posix::{anonymous}::default_syslog_sink_factory<CharT>::create_sink(const settings_section&) [with CharT = char; boost::log::v2_mt_posix::{anonymous}::default_syslog_sink_factory<CharT>::settings_section = boost::log::v2_mt_posix::basic_settings_section<char>]’ can be marked override [-Wsuggest-override]  
libs/log/src/syslog_backend.cpp:179:10: warning: ‘virtual void boost::log::v2_mt_posix::sinks::syslog_backend::implementation::native::send(boost::log::v2_mt_posix::sinks::syslog::level, const string_type&)’ can be marked override [-Wsuggest-override]  
libs/log/src/syslog_backend.cpp:432:10: warning: ‘virtual void boost::log::v2_mt_posix::sinks::syslog_backend::implementation::udp_socket_based::send(boost::log::v2_mt_posix::sinks::syslog::level, const string_type&)’ can be marked override [-Wsuggest-override]  
libs/log/src/text_file_backend.cpp:663:14: warning: ‘virtual void boost::log::v2_mt_posix::sinks::{anonymous}::file_collector::store_file(const boost::filesystem::path&)’ can be marked override [-Wsuggest-override]  
libs/log/src/text_file_backend.cpp:666:19: warning: ‘virtual uintmax_t boost::log::v2_mt_posix::sinks::{anonymous}::file_collector::scan_for_files(boost::log::v2_mt_posix::sinks::file::scan_method, const boost::filesystem::path&, unsigned int*)’ can be marked override [-Wsuggest-override]  
libs/log/src/threadsafe_queue.cpp:104:10: warning: ‘virtual bool boost::log::v2_mt_posix::aux::threadsafe_queue_impl_generic::try_pop(boost::log::v2_mt_posix::aux::threadsafe_queue_impl::node_base*&, boost::log::v2_mt_posix::aux::threadsafe_queue_impl::node_base*&)’ can be marked override [-Wsuggest-override]  
libs/log/src/threadsafe_queue.cpp:83:16: warning: ‘virtual boost::log::v2_mt_posix::aux::threadsafe_queue_impl::node_base* boost::log::v2_mt_posix::aux::threadsafe_queue_impl_generic::reset_last_node()’ can be marked override [-Wsuggest-override]  
libs/log/src/threadsafe_queue.cpp:91:10: warning: ‘virtual bool boost::log::v2_mt_posix::aux::threadsafe_queue_impl_generic::unsafe_empty()’ can be marked override [-Wsuggest-override]  
libs/log/src/threadsafe_queue.cpp:96:10: warning: ‘virtual void boost::log::v2_mt_posix::aux::threadsafe_queue_impl_generic::push(boost::log::v2_mt_posix::aux::threadsafe_queue_impl::node_base*)’ can be marked override [-Wsuggest-override]  
libs/log/src/timer.cpp:134:21: warning: ‘virtual boost::log::v2_mt_posix::attribute_value boost::log::v2_mt_posix::attributes::timer::impl::get_value()’ can be marked override [-Wsuggest-override]  
</pre>

---

## Comment 2

> Username: Lastique  
> Created at: 2020-04-13 08:33:03 UTC  
> Url: https://github.com/boostorg/log/issues/116#issuecomment-612807246  

https://github.com/boostorg/config/pull/329 needs to be merged first.

---

## Comment 3

> Username: EugeneZelenko  
> Created at: 2020-04-13 18:02:45 UTC  
> Url: https://github.com/boostorg/log/issues/116#issuecomment-613017526  

boostorg/config#329 was merged. See also  boostorg/wave#88 for Clang-tidy command line to fix warnings.

---

## Comment 4

> Username: EugeneZelenko  
> Created at: 2020-05-04 10:14:37 UTC  
> Url: https://github.com/boostorg/log/issues/116#issuecomment-623378758  

Please take a look at #120.
