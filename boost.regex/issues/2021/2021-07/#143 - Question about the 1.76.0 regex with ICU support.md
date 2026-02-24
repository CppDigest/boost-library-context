# #143 - Question about the 1.76.0 regex with ICU support [Closed]

> Username: wangqr  
> Created at: 2021-07-10 13:50:28 UTC  
> Updated at: 2021-10-10 18:31:21 UTC  
> Closed at: 2021-10-10 18:31:21 UTC  
> Url: https://github.com/boostorg/regex/issues/143  

Upgrading boost from 1.75.0 -> 1.76.0, regex becomes header only. Now if we use `boost/regex/icu.hpp` in 1.76.0, we'll need to link to icuin, otherwise we get a undefined reference to symbol error for [_ZN6icu_698Collator14createInstanceERKNS_6LocaleER10UErrorCode](https://github.com/boostorg/regex/blob/86f82635d4eb483be1b5cb72820dcf998cec48be/include/boost/regex/v5/icu.hpp#L54).  
  
Can we either:  
* Add something like `target_link_libraries(boost_regex INTERFACE ICU::in)` to the installed `boost_regex-config.cmake` if `BOOST_REGEX_USE_ICU` is defined when configure, or  
* Update the document (eg. https://www.boost.org/doc/libs/1_76_0/libs/regex/doc/html/boost_regex/install.html#boost_regex.install.building_with_unicode_and_icu_su), add instructions to link to icu when using boost::regex with icu support.  
  
Ref: wangqr/Aegisub#113

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-10-10 18:31:21 UTC  
> Url: https://github.com/boostorg/regex/issues/143#issuecomment-939530777  

Addressed in https://github.com/boostorg/regex/pull/161
