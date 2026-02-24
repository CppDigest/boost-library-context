# #73 - Release Notes for Boost.DateTime release 1.67.0 [Closed]

> Username: jeking3  
> Created at: 2018-01-19 17:37:29 UTC  
> Updated at: 2018-01-28 16:26:22 UTC  
> Closed at: 2018-01-28 16:26:22 UTC  
> Url: https://github.com/boostorg/date_time/issues/73  

* [phrase library..[@/libs/date_time/ DateTime]:]  
  * Fixed various year 2038 (32-bit) issues  
    ([ticket 2818]) ([ticket 2819]) ([ticket 3487]) ([ticket 4543]) ([ticket 8450]) ([ticket 9158]) ([ticket 9489]) ([ticket 10570]) ([ticket 12609])  
  * Fixed a binary serialization regression introduced in 1.66.0  
    ([github date_time 56]) ([github_pr date_time 58]) ([github_pr date_time 70])  
  * Updated the timezone database file  
    ([ticket 4430]) ([ticket 10087]) ([github_pr date_time 68])  
  * enhanced from_iso_string so it can read output of to_iso_string for special values  
    ([ticket 1078]) ([github_pr date_time 69])  
  * changed maximum supported year from 10000 to 9999 to resolve various issues  
    ([ticket 13159]) ([ticket 12630]) ([github_pr date_time 71])  
  * boost::date_time::period_parser::delimiter_strings did nothing  
    ([ticket 11142]) ([github_pr date_time 63])  
  * Ensure special values are not automatically translated to integral types  
    ([ticket 11168]) ([github_pr date_time 64])  
  * int_adapter is_signed should be const  
    ([ticket 12363]) ([github_pr date_time 60])  
  * boost::date_time::time_input_facet throws when using %j alone  
    ([ticket 12910]) ([github_pr date_time 59])  
  * Time input facet is not able to parse '%e' day  
    ([ticket 13194]) ([github_pr date_time 54])  
  * Improved github CI build environment for better project metrics and quality  
    ([github_pr date_time 52]) ([github_pr date_time 72])  
  * Fixed various compiler warnings  
    ([ticket 3606]) ([ticket 9882]) ([github_pr date_time 62])
