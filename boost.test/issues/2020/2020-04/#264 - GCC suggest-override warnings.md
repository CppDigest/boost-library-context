# #264 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 22:38:19 UTC  
> Updated at: 2022-02-27 19:46:34 UTC  
> Closed at: 2020-05-17 10:06:04 UTC  
> Url: https://github.com/boostorg/test/issues/264  

`BOOST_OVERRIDE` was added in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.  
  
Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted:  
  
<pre>  
./boost/test/impl/framework.ipp:1229:25: warning: ‘virtual bool boost::unit_test::framework::finalize_setup_phase(boost::unit_test::test_unit_id)::apply_decorators::test_suite_start(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/framework.ipp:1236:25: warning: ‘virtual bool boost::unit_test::framework::finalize_setup_phase(boost::unit_test::test_unit_id)::apply_decorators::visit(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/framework.ipp:277:21: warning: ‘virtual void boost::unit_test::framework::impl::name_filter::visit(const boost::unit_test::test_case&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/framework.ipp:283:21: warning: ‘virtual bool boost::unit_test::framework::impl::name_filter::test_suite_start(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/framework.ipp:297:21: warning: ‘virtual void boost::unit_test::framework::impl::name_filter::test_suite_finish(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/framework.ipp:323:21: warning: ‘virtual bool boost::unit_test::framework::impl::label_filter::visit(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/framework.ipp:351:21: warning: ‘virtual bool boost::unit_test::framework::impl::set_run_status::visit(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/framework.ipp:465:21: warning: ‘virtual void boost::unit_test::framework::impl::global_fixture_handle::setup()’ can be marked override [-Wsuggest-override]  
./boost/test/impl/framework.ipp:468:21: warning: ‘virtual void boost::unit_test::framework::impl::global_fixture_handle::teardown()’ can be marked override [-Wsuggest-override]  
./boost/test/impl/junit_log_formatter.ipp:400:13: warning: ‘virtual void boost::unit_test::output::junit_result_helper::visit(const boost::unit_test::test_case&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/junit_log_formatter.ipp:415:13: warning: ‘virtual bool boost::unit_test::output::junit_result_helper::test_suite_start(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/junit_log_formatter.ipp:474:21: warning: ‘virtual void boost::unit_test::output::junit_result_helper::test_suite_finish(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/results_collector.ipp:181:13: warning: ‘virtual void boost::unit_test::results_collect_helper::visit(const boost::unit_test::test_case&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/results_collector.ipp:205:13: warning: ‘virtual bool boost::unit_test::results_collect_helper::test_suite_start(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/results_reporter.ipp:62:13: warning: ‘virtual void boost::unit_test::results_reporter::{anonymous}::results_reporter_impl::visit(const boost::unit_test::test_case&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/results_reporter.ipp:67:13: warning: ‘virtual bool boost::unit_test::results_reporter::{anonymous}::results_reporter_impl::test_suite_start(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/results_reporter.ipp:77:13: warning: ‘virtual void boost::unit_test::results_reporter::{anonymous}::results_reporter_impl::test_suite_finish(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/unit_test_main.ipp:135:21: warning: ‘virtual void boost::unit_test::ut_detail::dot_content_reporter::visit(const boost::unit_test::test_case&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/unit_test_main.ipp:139:21: warning: ‘virtual bool boost::unit_test::ut_detail::dot_content_reporter::test_suite_start(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/unit_test_main.ipp:150:21: warning: ‘virtual void boost::unit_test::ut_detail::dot_content_reporter::test_suite_finish(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/unit_test_main.ipp:168:29: warning: ‘virtual bool boost::unit_test::ut_detail::labels_collector::visit(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/unit_test_main.ipp:70:21: warning: ‘virtual void boost::unit_test::ut_detail::hrf_content_reporter::visit(const boost::unit_test::test_case&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/unit_test_main.ipp:71:21: warning: ‘virtual bool boost::unit_test::ut_detail::hrf_content_reporter::test_suite_start(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/impl/unit_test_main.ipp:78:21: warning: ‘virtual void boost::unit_test::ut_detail::hrf_content_reporter::test_suite_finish(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:37:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_start(std::ostream&, boost::unit_test::counter_t)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:38:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:39:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_build_info(std::ostream&, bool)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:41:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::test_unit_start(std::ostream&, const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:42:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::test_unit_finish(std::ostream&, const boost::unit_test::test_unit&, long unsigned int)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:43:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::test_unit_skipped(std::ostream&, const boost::unit_test::test_unit&, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:45:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_exception_start(std::ostream&, const boost::unit_test::log_checkpoint_data&, const boost::execution_exception&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:46:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_exception_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:48:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_entry_start(std::ostream&, const boost::unit_test::log_entry_data&, boost::unit_test::unit_test_log_formatter::log_entry_types)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:49:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_entry_value(std::ostream&, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:50:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_entry_value(std::ostream&, const boost::unit_test::lazy_ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:51:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_entry_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:53:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::entry_context_start(std::ostream&, boost::unit_test::log_level)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:54:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::log_entry_context(std::ostream&, boost::unit_test::log_level, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/output/compiler_log_formatter.hpp:55:13: warning: ‘virtual void boost::unit_test::output::compiler_log_formatter::entry_context_finish(std::ostream&, boost::unit_test::log_level)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:102:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::log_start(std::ostream&, boost::unit_test::counter_t)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:103:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::log_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:104:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::log_build_info(std::ostream&, bool)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:106:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::test_unit_start(std::ostream&, const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:107:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::test_unit_finish(std::ostream&, const boost::unit_test::test_unit&, long unsigned int)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:108:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::test_unit_skipped(std::ostream&, const boost::unit_test::test_unit&, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:109:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::test_unit_aborted(std::ostream&, const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:110:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::test_unit_timed_out(std::ostream&, const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:112:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::log_exception_start(std::ostream&, const boost::unit_test::log_checkpoint_data&, const boost::execution_exception&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:113:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::log_exception_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:115:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::log_entry_start(std::ostream&, const boost::unit_test::log_entry_data&, boost::unit_test::unit_test_log_formatter::log_entry_types)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:118:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::log_entry_value(std::ostream&, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:119:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::log_entry_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:121:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::entry_context_start(std::ostream&, boost::unit_test::log_level)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:122:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::log_entry_context(std::ostream&, boost::unit_test::log_level, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:123:13: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::entry_context_finish(std::ostream&, boost::unit_test::log_level)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:126:25: warning: ‘virtual void boost::unit_test::output::junit_log_formatter::set_log_level(boost::unit_test::log_level)’ can be marked override [-Wsuggest-override]  
./boost/test/output/junit_log_formatter.hpp:139:26: warning: ‘virtual std::__cxx11::string boost::unit_test::output::junit_log_formatter::get_default_stream_description() const’ can be marked override [-Wsuggest-override]  
./boost/test/output/plain_report_formatter.hpp:39:13: warning: ‘virtual void boost::unit_test::output::plain_report_formatter::results_report_start(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/plain_report_formatter.hpp:40:13: warning: ‘virtual void boost::unit_test::output::plain_report_formatter::results_report_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/plain_report_formatter.hpp:42:13: warning: ‘virtual void boost::unit_test::output::plain_report_formatter::test_unit_report_start(const boost::unit_test::test_unit&, std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/plain_report_formatter.hpp:43:13: warning: ‘virtual void boost::unit_test::output::plain_report_formatter::test_unit_report_finish(const boost::unit_test::test_unit&, std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/plain_report_formatter.hpp:45:13: warning: ‘virtual void boost::unit_test::output::plain_report_formatter::do_confirmation_report(const boost::unit_test::test_unit&, std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:40:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::log_start(std::ostream&, boost::unit_test::counter_t)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:41:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::log_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:42:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::log_build_info(std::ostream&, bool)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:44:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::test_unit_start(std::ostream&, const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:45:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::test_unit_finish(std::ostream&, const boost::unit_test::test_unit&, long unsigned int)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:46:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::test_unit_skipped(std::ostream&, const boost::unit_test::test_unit&, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:48:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::log_exception_start(std::ostream&, const boost::unit_test::log_checkpoint_data&, const boost::execution_exception&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:49:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::log_exception_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:51:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::log_entry_start(std::ostream&, const boost::unit_test::log_entry_data&, boost::unit_test::unit_test_log_formatter::log_entry_types)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:53:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::log_entry_value(std::ostream&, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:54:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::log_entry_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:56:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::entry_context_start(std::ostream&, boost::unit_test::log_level)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:57:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::log_entry_context(std::ostream&, boost::unit_test::log_level, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_log_formatter.hpp:58:13: warning: ‘virtual void boost::unit_test::output::xml_log_formatter::entry_context_finish(std::ostream&, boost::unit_test::log_level)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_report_formatter.hpp:37:13: warning: ‘virtual void boost::unit_test::output::xml_report_formatter::results_report_start(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_report_formatter.hpp:38:13: warning: ‘virtual void boost::unit_test::output::xml_report_formatter::results_report_finish(std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_report_formatter.hpp:40:13: warning: ‘virtual void boost::unit_test::output::xml_report_formatter::test_unit_report_start(const boost::unit_test::test_unit&, std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_report_formatter.hpp:41:13: warning: ‘virtual void boost::unit_test::output::xml_report_formatter::test_unit_report_finish(const boost::unit_test::test_unit&, std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/output/xml_report_formatter.hpp:43:13: warning: ‘virtual void boost::unit_test::output::xml_report_formatter::do_confirmation_report(const boost::unit_test::test_unit&, std::ostream&)’ can be marked override [-Wsuggest-override]  
./boost/test/progress_monitor.hpp:37:21: warning: ‘virtual void boost::unit_test::progress_monitor_t::test_start(boost::unit_test::counter_t)’ can be marked override [-Wsuggest-override]  
./boost/test/progress_monitor.hpp:38:21: warning: ‘virtual void boost::unit_test::progress_monitor_t::test_aborted()’ can be marked override [-Wsuggest-override]  
./boost/test/progress_monitor.hpp:40:21: warning: ‘virtual void boost::unit_test::progress_monitor_t::test_unit_finish(const boost::unit_test::test_unit&, long unsigned int)’ can be marked override [-Wsuggest-override]  
./boost/test/progress_monitor.hpp:41:21: warning: ‘virtual void boost::unit_test::progress_monitor_t::test_unit_skipped(const boost::unit_test::test_unit&, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/progress_monitor.hpp:43:21: warning: ‘virtual int boost::unit_test::progress_monitor_t::priority()’ can be marked override [-Wsuggest-override]  
./boost/test/results_collector.hpp:124:25: warning: ‘virtual void boost::unit_test::results_collector_t::test_start(boost::unit_test::counter_t)’ can be marked override [-Wsuggest-override]  
./boost/test/results_collector.hpp:126:25: warning: ‘virtual void boost::unit_test::results_collector_t::test_unit_start(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/results_collector.hpp:127:25: warning: ‘virtual void boost::unit_test::results_collector_t::test_unit_finish(const boost::unit_test::test_unit&, long unsigned int)’ can be marked override [-Wsuggest-override]  
./boost/test/results_collector.hpp:128:25: warning: ‘virtual void boost::unit_test::results_collector_t::test_unit_skipped(const boost::unit_test::test_unit&, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/results_collector.hpp:129:25: warning: ‘virtual void boost::unit_test::results_collector_t::test_unit_aborted(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/results_collector.hpp:130:25: warning: ‘virtual void boost::unit_test::results_collector_t::test_unit_timed_out(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/results_collector.hpp:132:25: warning: ‘virtual void boost::unit_test::results_collector_t::assertion_result(boost::unit_test::assertion_result)’ can be marked override [-Wsuggest-override]  
./boost/test/results_collector.hpp:133:25: warning: ‘virtual void boost::unit_test::results_collector_t::exception_caught(const boost::execution_exception&)’ can be marked override [-Wsuggest-override]  
./boost/test/results_collector.hpp:135:25: warning: ‘virtual int boost::unit_test::results_collector_t::priority()’ can be marked override [-Wsuggest-override]  
./boost/test/test_framework_init_observer.hpp:37:25: warning: ‘virtual void boost::unit_test::framework_init_observer_t::test_start(boost::unit_test::counter_t)’ can be marked override [-Wsuggest-override]  
./boost/test/test_framework_init_observer.hpp:39:25: warning: ‘virtual void boost::unit_test::framework_init_observer_t::assertion_result(boost::unit_test::assertion_result)’ can be marked override [-Wsuggest-override]  
./boost/test/test_framework_init_observer.hpp:40:25: warning: ‘virtual void boost::unit_test::framework_init_observer_t::exception_caught(const boost::execution_exception&)’ can be marked override [-Wsuggest-override]  
./boost/test/test_framework_init_observer.hpp:41:25: warning: ‘virtual void boost::unit_test::framework_init_observer_t::test_aborted()’ can be marked override [-Wsuggest-override]  
./boost/test/test_framework_init_observer.hpp:43:25: warning: ‘virtual int boost::unit_test::framework_init_observer_t::priority()’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:113:29: warning: ‘virtual boost::unit_test::decorator::collector_t& boost::unit_test::decorator::stack_decorator::operator*() const’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:117:29: warning: ‘virtual void boost::unit_test::decorator::stack_decorator::apply(boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:118:29: warning: ‘virtual boost::unit_test::decorator::base_ptr boost::unit_test::decorator::stack_decorator::clone() const’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:131:29: warning: ‘virtual void boost::unit_test::decorator::label::apply(boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:132:29: warning: ‘virtual boost::unit_test::decorator::base_ptr boost::unit_test::decorator::label::clone() const’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:148:29: warning: ‘virtual void boost::unit_test::decorator::expected_failures::apply(boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:149:29: warning: ‘virtual boost::unit_test::decorator::base_ptr boost::unit_test::decorator::expected_failures::clone() const’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:165:29: warning: ‘virtual void boost::unit_test::decorator::timeout::apply(boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:166:29: warning: ‘virtual boost::unit_test::decorator::base_ptr boost::unit_test::decorator::timeout::clone() const’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:182:29: warning: ‘virtual void boost::unit_test::decorator::description::apply(boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:183:29: warning: ‘virtual boost::unit_test::decorator::base_ptr boost::unit_test::decorator::description::clone() const’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:199:29: warning: ‘virtual void boost::unit_test::decorator::depends_on::apply(boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:200:29: warning: ‘virtual boost::unit_test::decorator::base_ptr boost::unit_test::decorator::depends_on::clone() const’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:237:29: warning: ‘virtual void boost::unit_test::decorator::fixture_t::apply(boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:238:29: warning: ‘virtual boost::unit_test::decorator::base_ptr boost::unit_test::decorator::fixture_t::clone() const’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:284:29: warning: ‘virtual void boost::unit_test::decorator::precondition::apply(boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/decorator.hpp:285:29: warning: ‘virtual boost::unit_test::decorator::base_ptr boost::unit_test::decorator::precondition::clone() const’ can be marked override [-Wsuggest-override]  
./boost/test/tree/fixture.hpp:177:33: warning: ‘virtual void boost::unit_test::function_based_fixture::setup()’ can be marked override [-Wsuggest-override]  
./boost/test/tree/fixture.hpp:178:33: warning: ‘virtual void boost::unit_test::function_based_fixture::teardown()’ can be marked override [-Wsuggest-override]  
./boost/test/tree/global_fixture.hpp:49:21: warning: ‘virtual int boost::unit_test::global_configuration::priority()’ can be marked override [-Wsuggest-override]  
./boost/test/tree/test_case_counter.hpp:46:21: warning: ‘virtual void boost::unit_test::test_case_counter::visit(const boost::unit_test::test_case&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/test_case_counter.hpp:47:21: warning: ‘virtual bool boost::unit_test::test_case_counter::test_suite_start(const boost::unit_test::test_suite&)’ can be marked override [-Wsuggest-override]  
./boost/test/tree/test_case_template.hpp:139:24: warning: ‘virtual boost::unit_test::test_unit* boost::unit_test::ut_detail::template_test_case_gen_base::next() const’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:114:25: warning: ‘virtual void boost::unit_test::unit_test_log_t::test_start(boost::unit_test::counter_t)’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:115:25: warning: ‘virtual void boost::unit_test::unit_test_log_t::test_finish()’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:116:25: warning: ‘virtual void boost::unit_test::unit_test_log_t::test_aborted()’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:118:25: warning: ‘virtual void boost::unit_test::unit_test_log_t::test_unit_start(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:119:25: warning: ‘virtual void boost::unit_test::unit_test_log_t::test_unit_finish(const boost::unit_test::test_unit&, long unsigned int)’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:120:25: warning: ‘virtual void boost::unit_test::unit_test_log_t::test_unit_skipped(const boost::unit_test::test_unit&, boost::unit_test::const_string)’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:121:25: warning: ‘virtual void boost::unit_test::unit_test_log_t::test_unit_aborted(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:122:25: warning: ‘virtual void boost::unit_test::unit_test_log_t::test_unit_timed_out(const boost::unit_test::test_unit&)’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:124:25: warning: ‘virtual void boost::unit_test::unit_test_log_t::exception_caught(const boost::execution_exception&)’ can be marked override [-Wsuggest-override]  
./boost/test/unit_test_log.hpp:126:25: warning: ‘virtual int boost::unit_test::unit_test_log_t::priority()’ can be marked override [-Wsuggest-override]  
./boost/test/utils/lazy_ostream.hpp:65:29: warning: ‘std::ostream& boost::unit_test::lazy_ostream_impl<PrevType, T, StorageT>::operator()(std::ostream&) const [with PrevType = boost::unit_test::lazy_ostream_impl<boost::unit_test::lazy_ostream, char [11], const char (&)[11]>; T = std::__cxx11::basic_string<char>; StorageT = const std::__cxx11::basic_string<char>&; std::ostream = std::basic_ostream<char>]’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/errors.hpp:44:26: warning: ‘virtual const char* boost::runtime::param_error::what() const’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:325:29: warning: ‘boost::runtime::basic_param_ptr boost::runtime::parameter<ValueType, a, is_enum>::clone() const [with ValueType = boost::unit_test::log_level; boost::runtime::args_amount a = (boost::runtime::args_amount)0; bool is_enum = true; boost::runtime::basic_param_ptr = boost::shared_ptr<boost::runtime::basic_param>]’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:329:21: warning: ‘void boost::runtime::parameter<ValueType, a, is_enum>::produce_argument(boost::runtime::cstring, bool, boost::runtime::arguments_store&) const [with ValueType = boost::unit_test::log_level; boost::runtime::args_amount a = (boost::runtime::args_amount)0; bool is_enum = true; boost::runtime::cstring = boost::unit_test::basic_cstring<const char>]’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:333:21: warning: ‘void boost::runtime::parameter<ValueType, a, is_enum>::produce_default(boost::runtime::arguments_store&) const [with ValueType = boost::unit_test::log_level; boost::runtime::args_amount a = (boost::runtime::args_amount)0; bool is_enum = true]’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:369:29: warning: ‘virtual boost::runtime::basic_param_ptr boost::runtime::option::clone() const’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:374:21: warning: ‘virtual void boost::runtime::option::produce_argument(boost::runtime::cstring, bool, boost::runtime::arguments_store&) const’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:386:21: warning: ‘virtual void boost::runtime::option::produce_default(boost::runtime::arguments_store&) const’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:390:21: warning: ‘virtual void boost::runtime::option::cla_name_help(std::ostream&, boost::runtime::cstring, boost::runtime::cstring, bool) const’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:401:21: warning: ‘virtual void boost::runtime::option::value_help(std::ostream&) const’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:444:29: warning: ‘boost::runtime::basic_param_ptr boost::runtime::enum_parameter<EnumType, a>::clone() const [with EnumType = boost::unit_test::log_level; boost::runtime::args_amount a = (boost::runtime::args_amount)0; boost::runtime::basic_param_ptr = boost::shared_ptr<boost::runtime::basic_param>]’ can be marked override [-Wsuggest-override]  
./boost/test/utils/runtime/parameter.hpp:449:21: warning: ‘void boost::runtime::enum_parameter<EnumType, a>::value_help(std::ostream&) const [with EnumType = boost::unit_test::log_level; boost::runtime::args_amount a = (boost::runtime::args_amount)0; std::ostream = std::basic_ostream<char>]’ can be marked override [-Wsuggest-override]  
</pre>

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-04-27 16:41:54 UTC  
> Url: https://github.com/boostorg/test/issues/264#issuecomment-620100676  

Patch is in [test.txt](https://github.com/boostorg/test/files/4540923/test.txt).  
  
Also fix misspellings in comments.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2020-05-09 14:45:13 UTC  
> Url: https://github.com/boostorg/test/issues/264#issuecomment-626186771  

Thank you, building in the branch `topic/GH-264-GCC-override-warnings-suggestions`. Should we remove the `virtual` all the time? `BOOST_OVERRIDE` expands to nothing when not supported, so in those case maybe the `virtual` would help?  
  
Thanks also for the typo fixes

---

## Comment 3

> Username: EugeneZelenko  
> Created at: 2020-05-09 14:51:43 UTC  
> Url: https://github.com/boostorg/test/issues/264#issuecomment-626187714  

Clang-tidy modernize-use-override complains when both virtual and override present. I think C++11 is old enough, so override would be supported in majority of use cases.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2020-05-09 14:54:41 UTC  
> Url: https://github.com/boostorg/test/issues/264#issuecomment-626188085  

Good to know.  
  
C++03 support is still on for Boost.Test, but I guess if there is a mistake in the `virtual` chain, the `BOOST_OVERRIDE` will tell us on C++11 conformant compilers. There shouldn't be any problem in any case.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2020-05-17 10:06:04 UTC  
> Url: https://github.com/boostorg/test/issues/264#issuecomment-629773057  

In master, thanks!

---

## Comment 6

> Username: EugeneZelenko  
> Created at: 2020-05-17 13:50:15 UTC  
> Url: https://github.com/boostorg/test/issues/264#issuecomment-629801885  

Thank you for help!

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2020-05-17 18:54:25 UTC  
> Url: https://github.com/boostorg/test/issues/264#issuecomment-629843788  

Glad to help, but you did all the work! Thanks you!
