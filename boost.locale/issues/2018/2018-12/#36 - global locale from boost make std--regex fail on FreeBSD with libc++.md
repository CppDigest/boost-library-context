# #36 - global locale from boost make std::regex fail on FreeBSD with libc++ [Closed]

> Username: schorsch1976  
> Created at: 2018-12-21 06:07:53 UTC  
> Updated at: 2025-01-12 18:05:33 UTC  
> Closed at: 2025-01-12 18:04:15 UTC  
> Url: https://github.com/boostorg/locale/issues/36  

This is a dependent of   
https://github.com/boostorg/locale/issues/35  
but with more concentrated and more distilled information.  
  
[bug.txt](https://github.com/boostorg/locale/files/2701685/bug.txt)  
[result-csv.ods.tar.gz](https://github.com/boostorg/locale/files/2701686/result-csv.ods.tar.gz)  
  
The regex fails if one of this facets is set:  
 "all_characters , collation_facet, all_categories".   
  
	boost::locale::generator gen;  
	  
	// all_characters , collation_facet, all_categories trigger the bug",  
	gen.categories(collation_facet);  
	std::locale loc{ gen("de_DE.UTF-8") };  
	std::locale::global(loc);  
  
	std::regex re_datetime("^Date\\s*:[\\s\\t]*(\\d{4}\\-\\d{2}\\-\\d{2} "  
							"\\d{2}:\\d{2}:\\d{2}?)$",  
							std::regex_constants::icase);  
	std::smatch match;  
	if (std::regex_match(test, match, re_datetime) && match.size() > 1)  
		std::cout << "All ok" << std::endl;  
	else  
		std::cout << "Bug triggered" << std::endl;  
  
I setup a testprogram that tries every locale (192),every facet(12(no wchar_t on freebsd)), every backend(std/icu/posix) and icase on/off.  
  
The pivot analysis of the result shows, this happens on every locale (except posix/C) with facet  "all_characters , collation_facet, all_categories".   
  
This happens only on FreeBSD with clang and libc++.

---

## Comment 1

> Username: Flamefire  
> Created at: 2025-01-12 18:01:14 UTC  
> Updated at: 2025-01-12 18:05:33 UTC  
> Url: https://github.com/boostorg/locale/issues/36#issuecomment-2585852100  

Closing in favor of newer issue #249
