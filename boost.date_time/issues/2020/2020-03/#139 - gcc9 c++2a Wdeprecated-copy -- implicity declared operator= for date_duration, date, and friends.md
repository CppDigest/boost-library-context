# #139 - gcc9 c++2a Wdeprecated-copy -- implicity declared operator= for date_duration, date, and friends [Open]

> Username: JeffGarland  
> Created at: 2020-03-22 16:10:53 UTC  
> Updated at: 2020-03-22 16:10:53 UTC  
> Url: https://github.com/boostorg/date_time/issues/139  

Some warnings from develop with gcc9 in 2a mode.  
  
  
  
Warnings: teeks99-dkr-dg9-warn - date_time / gcc-9~c++2a~warn  
Rev b44058fda48dd994d394d4483472a7abe55031b8 / Sun, 22 Mar 2020 01:13:31 +0000  
testdate_duration  
  
../libs/date_time/test/gregorian/testdate_duration.cpp:44:22: warning: implicitly-declared 'constexpr boost::gregorian::date_duration& boost::gregorian::date_duration::operator=(const boost::gregorian::date_duration&)' is deprecated [-Wdeprecated-copy]  
                  
  
testdate_input_facet  
  
../boost/date_time/date_facet.hpp:509:23: warning: implicitly-declared 'boost::date_time::period_parser<boost::gregorian::date, char>& boost::date_time::period_parser<boost::gregorian::date, char>::operator=(const boost::date_time::period_parser<boost::gregorian::date, char>&)' is deprecated [-Wdeprecated-copy]  
../boost/date_time/date_facet.hpp:550:19: warning: implicitly-declared 'boost::date_time::special_values_parser<boost::gregorian::date, char>& boost::date_time::special_values_parser<boost::gregorian::date, char>::operator=(const boost::date_time::special_values_parser<boost::gregorian::date, char>&)' is deprecated [-Wdeprecated-copy]  
../boost/date_time/date_facet.hpp:635:12: warning: implicitly-declared 'constexpr boost::gregorian::date_duration& boost::gregorian::date_duration::operator=(const boost::gregorian::date_duration&)' is deprecated [-Wdeprecated-copy]  
../boost/date_time/date_facet.hpp:638:12: warning: implicitly-declared 'constexpr boost::gregorian::date_duration& boost::gregorian::date_duration::operator=(const boost::gregorian::date_duration&)' is deprecated [-Wdeprecated-copy]  
                  
  
testgenerators  
  
../boost/date_time/date_generators.hpp:463:11: warning: implicitly-declared 'constexpr boost::gregorian::date_duration& boost::gregorian::date_duration::operator=(const boost::gregorian::date_duration&)' is deprecated [-Wdeprecated-copy]  
../boost/date_time/date_generators.hpp:481:11: warning: implicitly-declared 'constexpr boost::gregorian::date_duration& boost::gregorian::date_duration::operator=(const boost::gregorian::date_duration&)' is deprecated [-Wdeprecated-copy]  
                  
  
testgreg_serialize  
  
../boost/date_time/gregorian/greg_serialize.hpp:121:6: warning: implicitly-declared 'constexpr boost::gregorian::date_duration& boost::gregorian::date_duration::operator=(const boost::gregorian::date_duration&)' is deprecated [-Wdeprecated-copy]  
                  
  
testgreg_serialize_xml  
  
../boost/date_time/gregorian/greg_serialize.hpp:121:6: warning: implicitly-declared 'constexpr boost::gregorian::date_duration& boost::gregorian::date_duration::operator=(const boost::gregorian::date_duration&)' is deprecated [-Wdeprecated-copy]  
                  
  
testlocal_time_iterator  
  
../boost/date_time/time_iterator.hpp:30:16: warning: implicitly-declared 'boost::local_time::local_date_time_base<>& boost::local_time::local_date_time_base<>::operator=(const boost::local_time::local_date_time_base<>&)' is deprecated [-Wdeprecated-copy]  
../boost/date_time/time_iterator.hpp:25:16: warning: implicitly-declared 'boost::local_time::local_date_time_base<>& boost::local_time::local_date_time_base<>::operator=(const boost::local_time::local_date_time_base<>&)' is deprecated [-Wdeprecated-copy]  
                  
  
testlocal_time_period  
  
../boost/date_time/period.hpp:176:12: warning: implicitly-declared 'boost::local_time::local_date_time_base<>& boost::local_time::local_date_time_base<>::operator=(const boost::local_time::local_date_time_base<>&)' is deprecated [-Wdeprecated-copy]  
../boost/date_time/period.hpp:177:12: warning: implicitly-declared 'boost::local_time::local_date_time_base<>& boost::local_time::local_date_time_base<>::operator=(const boost::local_time::local_date_time_base<>&)' is deprecated [-Wdeprecated-copy]
