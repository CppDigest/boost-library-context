# #149 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 19:03:54 UTC  
> Updated at: 2020-05-23 14:28:28 UTC  
> Closed at: 2020-05-23 14:28:28 UTC  
> Url: https://github.com/boostorg/date_time/issues/149  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/date_time/date_generators.hpp:101:14: warning: ‘date_type boost::date_time::partial_date<date_type>::get_date(boost::date_time::partial_date<date_type>::year_type) const [with date_type = boost::gregorian::date; boost::date_time::partial_date<date_type>::year_type = boost::gregorian::greg_year]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_generators.hpp:142:24: warning: ‘std::__cxx11::string boost::date_time::partial_date<date_type>::to_string() const [with date_type = boost::gregorian::date; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_generators.hpp:195:15: warning: ‘date_type boost::date_time::nth_kday_of_month<date_type>::get_date(boost::date_time::nth_kday_of_month<date_type>::year_type) const [with date_type = boost::gregorian::date; boost::date_time::nth_kday_of_month<date_type>::year_type = boost::gregorian::greg_year]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_generators.hpp:233:25: warning: ‘std::__cxx11::string boost::date_time::nth_kday_of_month<date_type>::to_string() const [with date_type = boost::gregorian::date; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_generators.hpp:270:15: warning: ‘date_type boost::date_time::first_kday_of_month<date_type>::get_date(boost::date_time::first_kday_of_month<date_type>::year_type) const [with date_type = boost::gregorian::date; boost::date_time::first_kday_of_month<date_type>::year_type = boost::gregorian::greg_year]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_generators.hpp:290:25: warning: ‘std::__cxx11::string boost::date_time::first_kday_of_month<date_type>::to_string() const [with date_type = boost::gregorian::date; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_generators.hpp:331:15: warning: ‘date_type boost::date_time::last_kday_of_month<date_type>::get_date(boost::date_time::last_kday_of_month<date_type>::year_type) const [with date_type = boost::gregorian::date; boost::date_time::last_kday_of_month<date_type>::year_type = boost::gregorian::greg_year]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_generators.hpp:351:25: warning: ‘std::__cxx11::string boost::date_time::last_kday_of_month<date_type>::to_string() const [with date_type = boost::gregorian::date; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:263:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_month_short(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::month_enum) const [with Config = boost::gregorian::greg_facet_config; charT = char; OutputIterator = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::month_enum = boost::date_time::months_of_year]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:263:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_month_short(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::month_enum) const [with Config = boost::gregorian::greg_facet_config; charT = wchar_t; OutputIterator = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::month_enum = boost::date_time::months_of_year]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:268:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_month_long(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::month_enum) const [with Config = boost::gregorian::greg_facet_config; charT = char; OutputIterator = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::month_enum = boost::date_time::months_of_year]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:268:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_month_long(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::month_enum) const [with Config = boost::gregorian::greg_facet_config; charT = wchar_t; OutputIterator = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::month_enum = boost::date_time::months_of_year]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:273:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_special_value(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::special_value_enum) const [with Config = boost::gregorian::greg_facet_config; charT = char; OutputIterator = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::special_value_enum = boost::date_time::special_values]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:273:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_special_value(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::special_value_enum) const [with Config = boost::gregorian::greg_facet_config; charT = wchar_t; OutputIterator = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::special_value_enum = boost::date_time::special_values]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:277:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_weekday_short(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::weekday_enum) const [with Config = boost::gregorian::greg_facet_config; charT = char; OutputIterator = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::weekday_enum = boost::date_time::weekdays]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:277:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_weekday_short(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::weekday_enum) const [with Config = boost::gregorian::greg_facet_config; charT = wchar_t; OutputIterator = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::weekday_enum = boost::date_time::weekdays]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:281:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_weekday_long(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::weekday_enum) const [with Config = boost::gregorian::greg_facet_config; charT = char; OutputIterator = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::weekday_enum = boost::date_time::weekdays]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:281:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_put_weekday_long(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&, boost::date_time::all_date_names_put<Config, charT, OutputIterator>::weekday_enum) const [with Config = boost::gregorian::greg_facet_config; charT = wchar_t; OutputIterator = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::weekday_enum = boost::date_time::weekdays]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:286:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_month_sep_char(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&) const [with Config = boost::gregorian::greg_facet_config; charT = char; OutputIterator = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<char, std::char_traits<char> >]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:286:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_month_sep_char(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&) const [with Config = boost::gregorian::greg_facet_config; charT = wchar_t; OutputIterator = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:291:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_day_sep_char(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&) const [with Config = boost::gregorian::greg_facet_config; charT = char; OutputIterator = std::ostreambuf_iterator<char, std::char_traits<char> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<char, std::char_traits<char> >]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:291:20: warning: ‘void boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_day_sep_char(boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type&) const [with Config = boost::gregorian::greg_facet_config; charT = wchar_t; OutputIterator = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >; boost::date_time::all_date_names_put<Config, charT, OutputIterator>::iter_type = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:296:30: warning: ‘boost::date_time::ymd_order_spec boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_date_order() const [with Config = boost::gregorian::greg_facet_config; charT = char; OutputIterator = std::ostreambuf_iterator<char, std::char_traits<char> >]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:296:30: warning: ‘boost::date_time::ymd_order_spec boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_date_order() const [with Config = boost::gregorian::greg_facet_config; charT = wchar_t; OutputIterator = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:301:33: warning: ‘boost::date_time::month_format_spec boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_month_format() const [with Config = boost::gregorian::greg_facet_config; charT = char; OutputIterator = std::ostreambuf_iterator<char, std::char_traits<char> >]’ can be marked override [-Wsuggest-override]  
./boost/date_time/date_names_put.hpp:301:33: warning: ‘boost::date_time::month_format_spec boost::date_time::all_date_names_put<Config, charT, OutputIterator>::do_month_format() const [with Config = boost::gregorian::greg_facet_config; charT = wchar_t; OutputIterator = std::ostreambuf_iterator<wchar_t, std::char_traits<wchar_t> >]’ can be marked override [-Wsuggest-override]  
./boost/date_time/time_facet.hpp:272:18: warning: ‘void boost::date_time::time_facet<time_type, CharT, OutItrT>::set_iso_format() [with time_type = boost::posix_time::ptime; CharT = char; OutItrT = std::ostreambuf_iterator<char, std::char_traits<char> >]’ can be marked override [-Wsuggest-override]  
./boost/date_time/time_facet.hpp:276:18: warning: ‘void boost::date_time::time_facet<time_type, CharT, OutItrT>::set_iso_extended_format() [with time_type = boost::posix_time::ptime; CharT = char; OutItrT = std::ostreambuf_iterator<char, std::char_traits<char> >]’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-04-16 17:42:27 UTC  
> Url: https://github.com/boostorg/date_time/issues/149#issuecomment-614796767  

So there's been a lot of changes to this code for boost 1.73 -- will have to look at this against that baseline to see if it's changed (likely not that much).

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-04-16 17:46:39 UTC  
> Url: https://github.com/boostorg/date_time/issues/149#issuecomment-614798942  

See also boostorg/wave#88 for receipt of automatic fixes with Clang-tidy.

---

## Comment 3

> Username: EugeneZelenko  
> Created at: 2020-05-23 04:52:31 UTC  
> Url: https://github.com/boostorg/date_time/issues/149#issuecomment-632985238  

See fixes in #155.

---

## Comment 4

> Username: EugeneZelenko  
> Created at: 2020-05-23 14:28:28 UTC  
> Url: https://github.com/boostorg/date_time/issues/149#issuecomment-633064665  

Fixes were merged.
